# VOYAGR — Architecture Overview

High-level technical architecture for VOYAGR. Written for product and engineering audiences — no implementation code.

---

## System Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                        iOS App                               │
│                   React Native + Expo                        │
│                                                              │
│  ┌─────────────┐  ┌──────────────┐  ┌────────────────────┐  │
│  │   Screens   │  │   Services   │  │    Cache Layer     │  │
│  │  Trip View  │  │  trips.ts    │  │  SQLite (offline)  │  │
│  │  Timeline   │  │  bookings.ts │  │  trips             │  │
│  │  Entry Req  │  │  validation  │  │  bookings          │  │
│  │  Directions │  │  affiliates  │  │  transit_dirs      │  │
│  └─────────────┘  └──────┬───────┘  └────────────────────┘  │
│                          │                   ↑               │
│                    Network ↓           Cache fallback        │
└──────────────────────────┼──────────────────────────────────┘
                           │
          ┌────────────────┼─────────────────┐
          ↓                ↓                 ↓
   ┌─────────────┐  ┌────────────┐  ┌──────────────────┐
   │  Supabase   │  │  External  │  │  Supabase Edge   │
   │  Postgres   │  │   APIs     │  │   Functions      │
   │  Auth       │  │            │  │                  │
   │  RLS        │  │ Aviation-  │  │ transit-dirs     │
   │             │  │ Stack      │  │ (proxies Claude) │
   │             │  │            │  │                  │
   │             │  │ Google     │  │ ANTHROPIC_KEY    │
   │             │  │ Places     │  │ (server secret)  │
   └─────────────┘  └────────────┘  └──────────────────┘
                                            ↓
                                    ┌───────────────┐
                                    │  Claude Haiku │
                                    │  (Anthropic)  │
                                    └───────────────┘
```

---

## Key Architectural Principles

### 1. Offline-First
Every feature that must work during a trip is cached to the device at setup time. The service layer is Supabase-first on network, SQLite-fallback on network errors. Auth errors are never caught by the offline fallback — a logged-out user is prompted to re-authenticate, not silently served stale data.

### 2. Secrets Never in the Bundle
API keys that must be kept private (Claude API key, validation API keys) never touch the app binary. They live as Supabase secrets and are accessed only from Edge Functions, which verify JWT before any sensitive call. The app bundle contains only public-scoped keys (Supabase anon key, PostHog ingest key) that are safe to expose by design.

### 3. Service Layer Separation
All Supabase interactions go through a thin service layer, not directly from screens. This keeps screens clean and made the offline cache drop-in possible in Phase 3 — the cache layer sits between the service and SQLite without touching any screen code.

### 4. Row-Level Security Everywhere
Every Supabase table has RLS enabled. User A cannot read User B's trips, bookings, or profile — enforced at the database level, not the application level. This holds even if application logic has a bug.

---

## Data Flow: Booking Validation

```
User fills booking form
        ↓
"Save" tapped
        ↓
Validate-first (blocks save):
  Flight → AviationStack API
  Hotel  → Google Places API
        ↓
  Pass? → Save to Supabase → Write to SQLite cache → Show "Verified" badge
  Fail? → Block save → Show error → User corrects
```

Validation is synchronous and blocking — the UX shows "Validating..." and the save button is disabled during the check. A booking that fails validation cannot be saved. This is a deliberate product decision, not a technical constraint.

---

## Data Flow: Transit Directions (Offline-Capable)

```
User taps "Get Directions"
        ↓
Check SQLite cache (trip_id)
        ↓
Cache hit?  → Serve immediately (works offline)
Cache miss? → Call Supabase Edge Function
                    ↓
              Verify JWT
                    ↓
              Build prompt (sorted bookings, capped at 30)
                    ↓
              Call Claude Haiku API
                    ↓
              Strip markdown, parse JSON
                    ↓
              Return to client → Write to SQLite cache → Display
        ↓
Cache invalidates on any booking create / update / delete
```

The API key for Claude never leaves the server. If the Edge Function is called without a valid JWT, it returns 401 before any Anthropic call is made.

---

## Offline Cache Architecture

SQLite database on device, 4 tables:

| Table | Contents | Cache Behavior |
|-------|---------|----------------|
| `trips` | All user trips | Write-through on every fetch; orphan cleanup on sync |
| `bookings` | All bookings per trip | Write-through on fetch; immediate write on create/update; delete on remove |
| `transit_directions` | AI-generated directions per trip | Written on generation; invalidated on booking mutation |
| `sync_meta` | Last sync timestamps per key | Updated on every successful Supabase fetch |

**Fallback logic:** Network errors fall back to SQLite. Auth errors (JWT expired, 401, 403) propagate and force re-authentication. HTTP 5xx errors propagate — degraded service is distinguished from no connectivity.

---

## Security Model

| Concern | Approach |
|---------|---------|
| User data isolation | RLS on every table; cross-user reads return 0 rows by policy |
| API key exposure | Sensitive keys in Supabase secrets only; public keys scoped by design |
| Transit direction API | JWT verified in Edge Function before any Anthropic call |
| Auth tokens on device | expo-secure-store (iOS Keychain) for production; not AsyncStorage |
| Raw import data | Auto-purged 24h after import; never persisted beyond processing window |
| Validation responses | Trigger strips raw API response to `{status, verified, error_code}` on write |
| Analytics | No PII in PostHog events; no confirmation codes, passport numbers, or names |

---

## Subscription and Access Control

Access gates are managed through a `useProAccess()` hook that checks two sources:

1. **Beta access table** (admin-managed) — rows inserted directly in Supabase for TestFlight testers
2. **RevenueCat entitlement** (Phase 5) — validated App Store subscription receipt

Pro features are gated in the UI only when both checks return false. Beta testers get full Pro access without a subscription by design — no code change needed per tester.

---

## Observability

| Tool | What It Monitors |
|------|-----------------|
| PostHog | Feature adoption, funnel conversion, offline reliability rate, session starts |
| Sentry | Crash-free session rate (target ≥ 99.5%), error rate by feature, performance |
| Supabase Edge Function logs | Transit direction generation time, API error rate, cache hit rate |

Five PostHog dashboards defined: Activation, Engagement, Retention, Quality, Growth. Alerts configured for offline reliability drops below 95% and import failure spikes.
