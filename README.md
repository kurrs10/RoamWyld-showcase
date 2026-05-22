# VOYAGR

**Your trip. Any country. No WiFi needed.**

> iOS travel companion app — shipping June 2026  
> Built by [Kirsten Evans](https://www.kirstenmoberly.com), Product Manager

---

## The Problem

Self-planned multi-country trips are a logistical nightmare. Travelers spend more time managing their trip than experiencing it — digging for confirmation numbers at airport desks, Googling visa requirements across five government websites, opening Google Maps to get a vague "take the metro" with no specifics, and texting each other "wait, what's the hotel address again?"

No single app solves this. TripIt organizes your itinerary but can't tell you which train platform to stand on. Google Maps gives directions but doesn't know your booking. Dozens of tabs, a notes app, and a handful of screenshots — that's the current state of the art.

VOYAGR is the app that should have existed the whole time.

---

## Solution

A single iOS app that:
- **Imports** bookings from Gmail, PDF uploads, or manual entry — and validates them against real flight and hotel data
- **Guides** travelers step-by-step through every transit connection on their itinerary, with specific train lines, platform numbers, and ticket costs
- **Informs** travelers of entry requirements, visa rules, and Schengen day limits — per passport, for mixed-nationality groups
- **Works fully offline** — all critical content cached to the device at trip setup, before you ever board the plane

---

## Core Features

### Smart Import + Validation
- Connect Gmail or Outlook — auto-detect travel confirmations, review before importing
- Manual entry for any booking type: flights, hotels, activities
- **Booking validation:** flight confirmation numbers verified against live airline data; hotels validated to exist via Google Places — fake or mistyped bookings are caught before they matter

### Step-by-Step Transit Directions
- AI-generated directions for every travel segment on the itinerary
- Specific: train line, platform number, estimated cost, local tips
- Generated on demand, cached offline — works in airplane mode, underground, abroad

### Entry Requirements (Per Passport)
- Per-passport checklists for every destination on the trip
- Handles mixed-nationality groups (e.g., US + UK passport holders have different rules post-Brexit)
- Schengen day tracker with 80-day warning threshold
- Passport validity checks and requirement alerts

### Offline-First Architecture
All critical content works without internet:
- Full itinerary, confirmation numbers, addresses
- Transit directions (pre-cached at generation time)
- Entry requirements and passport checklists
- Emergency numbers, embassy contacts
- Currency + tipping guides
- Language basics (30–40 phrases per destination, contextually surfaced)

### Group / Couple Mode
- Shared itinerary with color-coded traveler views
- Per-person passport profiles and insurance storage
- Group invite viral loop: share a trip link, invited traveler downloads the app and lands directly in the shared itinerary

---

## Why It's Defensible

| Feature | TripIt Pro | Wanderlog | Rome2Rio | VOYAGR |
|---------|-----------|----------|---------|--------|
| Booking import | Strong | Moderate | None | Strong + **validation** |
| Booking validation | None | None | None | **Yes** |
| Step-by-step transit | None | None | Partial | **Yes, offline** |
| Entry requirements | None | None | External link | **Yes, per-passport** |
| Schengen tracking | None | None | None | **Yes** |
| Offline completeness | Partial | Partial (paywall) | None | **Full** |

The four-feature combination — validation + entry requirements + offline transit + full offline parity — doesn't exist in any current product. Each exists somewhere in isolation. VOYAGR is the first to integrate them into a single trip companion tied to the user's actual bookings.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Mobile | React Native + Expo SDK 54 (iOS) |
| Backend / Auth | Supabase (Postgres, Row-Level Security, Edge Functions) |
| Offline storage | expo-sqlite (WAL mode, write-through cache) |
| AI — transit directions | Claude Haiku via Supabase Edge Function |
| Flight validation | AviationStack API |
| Hotel validation | Google Places API |
| Email import | Gmail OAuth + REST API |
| Analytics | PostHog |
| Error monitoring | Sentry |
| Subscriptions | RevenueCat (StoreKit2) |

---

## Product Metrics

Success is defined across five areas:

| Category | Key Metric | Target |
|----------|-----------|--------|
| Activation | Trip setup completion rate | > 60% |
| Engagement | Daily active use during active trips | > 50% of travel days |
| Retention | Return trip creation within 6 months | > 25% |
| Quality | Crash-free session rate | ≥ 99.5% (Sentry) |
| Growth | Group invite → new install conversion | > 15% |

Full instrumentation plan: all key events mapped to PostHog, 5 dashboards defined, alerts configured for reliability drops.

---

## Build Approach

Built in 5 phases over 7 weeks using an AI-assisted product development workflow:

| Phase | Dates | Focus |
|-------|-------|-------|
| 1 — Foundation | May 13–19 | Scaffold, auth, manual entry, timeline |
| 2 — Import + Validation | May 20 – June 2 | Email import, booking validation |
| 3 — Differentiators | June 3–16 | Transit directions, entry requirements, offline cache |
| 4 — Companion Features | June 17–23 | Emergency info, currency, language, group mode |
| 5 — Polish + Launch | June 24–30 | Analytics, Sentry, TestFlight, App Store |

Each phase ends with a structured **demo panel review** across six disciplines (mobile engineering, architecture, UX research, UX design, QA, product strategy) — feedback is logged and incorporated before the phase closes. No phase closes without a demo panel and DEVLOG entry.

See [DEVLOG.md](DEVLOG.md) for the full build log with decisions, tradeoffs, and panel feedback.

---

## Monetization

**VOYAGR Free:** 1 active trip, booking validation, entry requirements, emergency info, first transit direction set.

**VOYAGR Pro — $14.99/year (launch pricing):** Unlimited trips, all transit directions, group mode, AI suggestions, insurance storage, Airalo eSIM recommendations.

Pricing strategy: launch at $14.99/year to build reviews and trust, increase to $24.99 after 200 reviews at 4.4+ stars. Full pricing rationale in the [product strategy doc](STRATEGY.md).

---

## Status

- [x] Phases 1–3 complete and ahead of schedule
- [ ] Phase 4 — Companion Features (June 17–23)
- [ ] Phase 5 — Polish + Launch (June 24–30)
- [ ] TestFlight beta
- [ ] App Store submission

---

## About the Builder

Built by **Kirsten Evans** — 9+ years shipping products at Capital One and Navy Federal Credit Union across fraud systems, mobile apps, cloud platforms, and AI tools.

VOYAGR is a solo end-to-end product project: market research, product spec, competitive analysis, technical architecture decisions, go-to-market strategy, and full build execution — demonstrating what a PM can own from concept to App Store.

[Portfolio](https://www.kirstenmoberly.com) · [LinkedIn](https://www.linkedin.com/in/kirsten-evans27) · [GitHub](https://github.com/kurrs10)
