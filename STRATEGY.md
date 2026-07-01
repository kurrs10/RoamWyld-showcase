# Roam Wyld — Product Strategy

Competitive positioning, monetization model, risk register, and quality/security standards.

---

## Competitive Positioning

### The Market Gap Roam Wyld Fills

No single app currently combines these four features:
1. **Booking validation** against live flight and hotel APIs
2. **Per-passport entry requirements** with mixed-nationality Schengen day tracking
3. **Step-by-step transit directions** tied to the actual itinerary and cached offline
4. **Full offline parity** — all static content available without internet

Individually, partial versions exist (Rome2Rio for routing, Sherpa for visa info, TripIt for email import) — but none are integrated into a single trip companion that knows your specific bookings.

### Competitor Matrix

| Feature | TripIt Pro | Wanderlog Plus | TripCase | Rome2Rio | AI Itinerary Startups | Roam Wyld |
|---------|-----------|---------------|---------|---------|----------------------|--------|
| Email/booking import | Strong | Moderate | Strong (corporate) | None | None | Strong + validation |
| Booking validation | None | None | None | None | None | Yes |
| Step-by-step transit | None | None | None | Partial (no platform) | None | Yes, offline |
| Entry requirements | None | None | None | External link only | None | Yes, per-passport |
| Schengen tracking | None | None | None | None | None | Yes |
| Offline completeness | Partial | Partial (paywall) | Partial | None | None | Full |
| AI features | None | Generic suggestions | None | None | Generation only | Cached, itinerary-tied |
| Group mode | Basic sharing | Strong collaboration | None | None | None | Yes (color-coded) |
| App Store rating | ~4.8 ★ | ~4.7 ★ | ~4.2 ★ | ~4.5 ★ | Varies | Target 4.5+ |
| Pricing | $49/yr | $39.99/yr | Free | Free | Free/varies | $24.99/yr |

### Pre-Launch Competitive Verification Checklist

Before finalizing App Store copy, verify live:
- [ ] Wanderlog's current offline depth (most likely to have closed gaps since mid-2025)
- [ ] Any new AI travel companion apps launched between Aug 2025 and launch date
- [ ] Apple's iOS travel feature expansion in latest iOS release
- [ ] Sherpa.io accuracy for 5 key routes (benchmark for entry requirements quality)

---

## Monetization Model

### Tier Structure

**Roam Wyld Free (permanent)**
- 1 active trip at a time
- Manual entry + email import
- Booking validation (flights + hotels)
- Entry requirements and passport checklist
- Emergency info, language basics, currency (all offline)
- Basic push alerts (flight/hotel reminders)
- Transit directions: **first trip free** (conversion mechanic)

**Roam Wyld Pro — $14.99/year or $1.99/month (launch pricing)**
- Unlimited trips
- All transit directions (Claude AI, cached offline)
- Group/couple mode (trip creator; invitees view free — keeps viral loop intact)
- Schengen tracker with proactive alerts
- AI free-time suggestions (pre-cached offline)
- Travel insurance storage + contextual surfacing
- Airalo eSIM recommendations (affiliate-linked, country pre-filtered)
- Expense splitting (v1.1)
- AI packing list (v1.1)

### Pricing Strategy

**Launch pricing: $1.99/month · $14.99/year**

- $14.99/year is the headline offer — frame as "Save 37%" vs. monthly equivalent ($23.88)
- $1.99/month exists as an entry point, not the primary pitch
- Lead with annual on the App Store product page

**Why not $3.99/month at launch:** Zero reviews means zero social proof. At $3.99/month the annual math hits $47.88 before the user has seen a single review from a stranger. At $1.99/month / $14.99/year, the decision feels like "try it before everyone knows about it."

**Free trial:** 30 days on monthly, 14 days on annual. Longer than typical because Roam Wyld's value is trip-contextual — a user with no upcoming trip won't see it in 7 days. Build a day-20 prompt: "You have 10 days left — create your first trip to see Roam Wyld in action."

**Price increase triggers:**

| Milestone | New Price | When |
|-----------|----------|------|
| 200 reviews at 4.4+ stars OR 6 months post-launch | $2.99/month · $24.99/year | ~Q4 2026 |
| 500 reviews at 4.5+ stars | $3.99/month · $34.99/year | ~2027 |
| v1.1 feature release (expense splitting + packing list) | Tie price increase to release | Narrative: "More powerful, price updated to match" |

Communicate raises 30 days in advance in-app. Frame it as earned, not opportunistic: "Locking in your current rate before the price increase."

**Do not offer lifetime purchase at launch.** Rewards your most loyal users by capping your revenue from them. If you want to reward early adopters, let them keep their current rate forever when prices rise.

### Group Invite Viral Loop

When a second traveler is added to a trip:
1. Trip owner gets a shareable invite link (deep link to the shared trip)
2. Invitee downloads Roam Wyld, opens the trip automatically
3. If invitee is on free tier, show upgrade prompt: *"Your travel partner uses Roam Wyld Pro — upgrade to unlock transit directions for your shared trip"*
4. Social proof from a trusted travel partner converts at 2–3× a cold paywall

This is the primary organic acquisition channel — every group trip is a free user acquisition event.

### Beta User Access

5–10 beta testers get free Pro access via the `beta_access` Supabase table (migration 003).

**To grant access (no code change needed):**
1. User creates their Roam Wyld account (gets a `user_id` in Supabase auth)
2. In Supabase Dashboard → Table Editor → `beta_access` → Insert row:
   - `user_id`: their UUID from auth.users
   - `granted_by`: your email
   - `expires_at`: leave null for indefinite, or set a date
   - `notes`: "beta cohort 1" or similar
3. User immediately gets full Pro access on next app open

The `useProAccess()` hook in `src/hooks/useProAccess.ts` handles this transparently — Pro features unlock when a valid beta row exists, no different from a paid subscription from the user's perspective.

### Conversion Mechanic

Give free users one full transit direction set on their first trip, no credit card. The paywall hits on trip 2, after they've already experienced the feature. By then the "should I pay $14.99/year" question answers itself.

### Affiliate Revenue

| Partner | Commission | Placement | Priority |
|---------|-----------|-----------|---------|
| Airalo (eSIM) | 10–18% per purchase (~$2–4/conversion) | 3–5 days before each destination; country pre-filtered | High — build in Phase 4 |
| SafetyWing / World Nomads (insurance) | $10–25 per policy | Trip setup → "Store insurance OR purchase one" | Medium |
| Hotel/booking affiliates | Varies | **Do not pursue at launch** — conflicts with neutral positioning | Deferred |

Register for Airalo affiliate program before Phase 4 ends — approval takes a few days.

### What Must Never Be Paywalled

- Booking validation — trust foundation; failure causes emotional attribution to Roam Wyld
- Entry requirements — safety-critical; paywalling visa info risks 1-star reviews citing danger
- Emergency info — never paywall ambulance numbers
- Core trip timeline view — a user mid-trip cannot be blocked from their own booking data
- First transit direction use — this is the conversion driver; charging before the experience kills it
- Basic flight/hotel push alerts — "your flight is tomorrow" is utility, not premium

### Monetization Timeline

| Phase | Action |
|-------|--------|
| TestFlight (June 30–August 2026) | Free, no paywall. Use PostHog to confirm which features drive most engagement |
| App Store launch (August–September 2026) | Introduce free/paid split. Frame in store description and first-run experience |
| 1,000 users | Review PostHog conversion data; adjust tier if needed before paid acquisition |
| Post-launch | Consider Family/Teams tier if group invite rate > 30% |

### Revenue Model (Year 1 Estimate at 5,000 Users)

- 15% Pro conversion → 750 × $24.99 = **$18,742/yr**
- Airalo affiliate (20% of Pro, ~$2 avg) → **$300/yr**
- Insurance affiliate (5% of Pro, ~$15 avg) → **$562/yr**
- **Total: ~$19,600/yr** — covers Claude API costs; validates model before paid acquisition

---

## Risk Register

### Critical Risks (P0)

**R1 — Transit Direction Quality**
- **Risk:** Claude Haiku-generated directions vary significantly by city. London and Tokyo: excellent. Secondary cities (Tbilisi, Cartagena, Cluj): potentially vague or wrong.
- **Impact:** Users revert to Google Maps; feature becomes a demo, not daily use; 1-star reviews citing wrong directions
- **Mitigation:** Quality-test in 5+ cities before launch (London, Tokyo, Rome, Bangkok + one secondary). Add "Directions are AI-generated and may not reflect real-time conditions" disclaimer. Give users a way to report bad directions.
- **Owner:** Phase 3/4 testing

**R2 — App Store Rejection**
- **Risk:** First submission is rejected, costing 24–48 hours minimum per cycle
- **Common causes for travel apps:** Missing privacy policy URL, demo account not provided, placeholder content visible, Sign in with Apple missing when other OAuth offered
- **Mitigation:** Run full app-store-specialist checklist before submission. Ensure privacy policy is live. Provide demo account credentials in review notes. No "coming soon" visible to reviewer.
- **Owner:** Phase 5, pre-submission

**R3 — June 30 Deadline**
- **Risk:** Phase 4 companion features (group mode, emergency info, language basics) run long, delaying TestFlight
- **Never-cut:** Offline caching, transit directions, entry requirements, booking validation
- **Safe cuts if behind:** eSIM recommendations, expense splitting (use Splitwise reference), weather (defer to v1.1)
- **Mitigation:** Timebox group mode to 3 days maximum per ROADMAP; cut eSIM if needed

**R4 — Claude API Cost at Scale**
- **Risk:** Transit directions are billed per-call. At 10,000 users with 2 trips/year each = 20,000 calls. At ~$0.02/call = $400/year — manageable. At 100K users = $4,000/year, still manageable if Pro conversion holds.
- **Mitigation:** Transit directions are Pro-only (or first-trip-free then Pro). Cache aggressively — never call the API twice for the same trip unless user regenerates. Log `is_cache_hit` in PostHog.
- **Owner:** Monitor via PostHog + Supabase Edge Function logs

### High Risks (P1)

**R5 — Wanderlog Closes the Gap**
- **Risk:** Wanderlog is well-funded and iterating fast. If they add deep offline caching and entry requirements before Roam Wyld launches, the differentiation narrative weakens.
- **Mitigation:** Verify Wanderlog's current feature set 30 days before App Store submission. If they've added entry requirements, double down on messaging about booking validation and Schengen tracking — those are harder to copy.

**R6 — Third-Party API Reliability**
- **Risk:** AviationStack or Google Places outage breaks booking validation. App appears broken.
- **Mitigation:** Validation failures degrade gracefully — show "Unable to verify, please check manually" rather than blocking. Never block trip creation on validation failure. Log all API errors to Sentry.

**R7 — GDPR / Privacy Compliance**
- **Risk:** EU users download the app; GDPR obligations apply. No DPAs signed, no data residency configured.
- **Mitigation:** Before App Store launch: (1) Privacy policy published at live URL, (2) DPAs signed with Supabase, PostHog, Sentry, Anthropic, (3) Account deletion cascade verified, (4) PostHog configured for EU data residency if targeting EU market.

**R8 — iOS Permission Denial Rate**
- **Risk:** Users deny push notifications on first ask. If > 50% deny, alert engagement rate tanks and one of the core retention metrics becomes unmeasurable.
- **Mitigation:** Don't request push notification permission at app launch. Request it at the moment the user enables their first alert, with a clear explanation of what they'll receive. Context-at-ask significantly improves grant rates.

### Medium Risks (P2)

**R9 — Solo Developer Key-Person Risk**
- **Risk:** Single point of failure for maintenance, bug fixes, and App Store updates
- **Mitigation:** CLAUDE.md, DEVLOG.md, and inline code documentation allow Claude Code to continue the build from any starting point. No knowledge locked in one person's head.

**R10 — App Store Rating Below 4.5**
- **Risk:** < 4.5 stars suppresses organic App Store discovery; Roam Wyld's target is 4.5+ within 90 days
- **Mitigation:** Solicit reviews at high-satisfaction moments: after a trip is completed, after offline directions work without internet, after an entry requirement alert is useful. Never prompt for a review when the user is in an error state.

**R11 — Email Import Parsing Failures**
- **Risk:** New confirmation email formats from airlines/hotels not in the parser training set cause import failures; users lose trust in the import feature
- **Mitigation:** Graceful fallback to manual entry. Log all parse failures with error type to PostHog (`import_failed`). Prioritize fixing top-failed formats in v1.1 based on data.

---

## Quality & Security Standards

### Performance Targets

| Metric | Target | Measurement |
|--------|--------|-------------|
| Cold launch to usable | < 3 seconds | Sentry performance monitoring |
| Trip screen load (cached) | < 500ms | Sentry |
| Offline feature load | < 300ms | Manual testing + PostHog `offline_feature_succeeded` |
| Transit directions generation | < 8 seconds | Supabase Edge Function logs |
| Crash-free session rate | ≥ 99.5% | Sentry |

### Security Non-Negotiables

1. **RLS on every table** — test with a second test user; confirm cross-user reads return 0 rows
2. **ANTHROPIC_API_KEY only in Supabase secrets** — never in app bundle or git history
3. **Service role key never in app bundle** — anon key only in `EXPO_PUBLIC_*`
4. **JWT verification in every Edge Function** before any data access or API call
5. **`expo-secure-store` for auth tokens** — never AsyncStorage
6. **No PII in PostHog events** — no confirmation codes, passport numbers, or full names
7. **`raw_import_data` purged within 24h** — migration 002 trigger handles this

### Pre-TestFlight Quality Checklist

- [ ] All NEVER-CUT features verified working offline (trip timeline, entry requirements, transit directions, emergency info)
- [ ] Cross-user data isolation tested — user A cannot read user B's trips
- [ ] Booking validation tested with both valid and invalid confirmation numbers
- [ ] Transit directions tested in at least 3 cities; disclaimer visible
- [ ] App runs on physical iPhone (not just simulator)
- [ ] No crash on network disconnect mid-session
- [ ] Account deletion tested — confirms full cascade (Supabase auth + trips + bookings + SQLite)
- [ ] PostHog instrumentation verified in Live Events stream — no PII visible
- [ ] Sentry crash reporting verified with a test crash
- [ ] Privacy policy live at public URL

### App Store Submission Checklist

See `~/.claude/agents/app-store-specialist.md` for full checklist. Key items:
- Privacy policy URL live and accessible
- Demo account credentials in review notes (if app requires login)
- Screenshots match actual app UI — no mockups
- App name in binary matches App Store Connect exactly
- Export compliance: HTTPS = exempt
- Sign in with Apple required if other OAuth login offered

---

## Strategic Priorities Before Launch

1. **Airalo affiliate** — register now (takes days); wire into Phase 4 eSIM feature from the start
2. **Verify Wanderlog's current feature set** — do this 4 weeks before App Store submission
3. **Transit direction quality testing** — test in 5 cities before marketing the feature
4. **Privacy policy** — must be live at a public URL before App Store submission; write it now
5. **DPAs** — sign with Supabase, PostHog, Sentry, Anthropic before EU launch
6. **Push notification permission timing** — request at first alert setup, not app launch
7. **Review request timing** — request at trip completion or offline success moments, never at errors
