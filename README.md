# Roam Wyld

**Your trip. Any country. No WiFi needed.**

> iOS travel companion app — shipping June 2026  
> Built by [Kirsten Evans](https://www.kirstenmoberly.com), Product Manager

---

## The Problem

Self-planned multi-country trips are a logistical nightmare. Travelers spend more time managing their trip than experiencing it — digging for confirmation numbers at airport desks, Googling visa requirements across five government websites, opening Google Maps to get a vague "take the metro" with no specifics, and texting each other "wait, what's the hotel address again?"

No single app solves this. TripIt organizes your itinerary but can't tell you which train platform to stand on. Google Maps gives directions but doesn't know your booking. Dozens of tabs, a notes app, and a handful of screenshots — that's the current state of the art.

Roam Wyld is the app that should have existed the whole time.

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
- Schengen day tracker with 3-tier warning system
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

| Feature | TripIt Pro | Wanderlog | Rome2Rio | Roam Wyld |
|---------|-----------|----------|---------|-----------|
| Booking import | Strong | Moderate | None | Strong + **validation** |
| Booking validation | None | None | None | **Yes** |
| Step-by-step transit | None | None | Partial | **Yes, offline** |
| Entry requirements | None | None | External link | **Yes, per-passport** |
| Schengen tracking | None | None | None | **Yes** |
| Offline completeness | Partial | Partial (paywall) | None | **Full** |

The four-feature combination — validation + entry requirements + offline transit + full offline parity — doesn't exist in any current product. Each exists somewhere in isolation. Roam Wyld is the first to integrate them into a single trip companion tied to the user's actual bookings.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Mobile | React Native + Expo (iOS) |
| Backend | Supabase (Postgres + Edge Functions + Auth) |
| AI | Claude API (Anthropic) — transit directions |
| Flight validation | AviationStack API |
| Hotel validation | Google Places API |
| Analytics | PostHog |
| Error monitoring | Sentry |
| Subscriptions | RevenueCat |
| Offline storage | expo-sqlite |

---

## Metrics & Success Definition

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

Each phase ends with a structured **demo panel review** across twelve disciplines (mobile engineering, architecture, UX research, UX design, QA, product strategy, legal/privacy, security, data analytics, App Store, product owner, QA engineer) — feedback is logged and incorporated before the phase closes. No phase closes without a demo panel and DEVLOG entry.

See [DEVLOG.md](DEVLOG.md) for the full build log with decisions, tradeoffs, and panel feedback.

---

## Monetization

**Roam Wyld Free:** Trip & booking management, entry requirements, currency converter, emergency info, flight validation, offline access.

**Roam Wyld Pro — $4.99/mo · $29.99/yr:** AI transit directions, Gmail booking import, unlimited trips, priority support. 14-day free trial on both plans.

---

## Status

- [x] Phase 1 — Foundation ✓
- [x] Phase 2 — Import + Validation ✓
- [x] Phase 3 — Differentiators ✓ (transit directions, entry requirements, offline caching)
- [ ] Phase 4 — Companion Features (June 17–23)
- [ ] Phase 5 — Polish + Launch (June 24–30)
- [ ] TestFlight beta (target June 20)
- [ ] App Store submission (target June 27)

---

## Legal

Privacy Policy and Terms of Service: [RoamWyld-legal](https://github.com/kurrs10/RoamWyld-legal)

---

## About the Builder

Built by **Kirsten Evans** — 9+ years shipping products at Capital One and Navy Federal Credit Union across fraud systems, mobile apps, cloud platforms, and AI tools.

Roam Wyld is a solo end-to-end product project: market research, product spec, competitive analysis, technical architecture decisions, go-to-market strategy, and full build execution — demonstrating what a PM can own from concept to App Store.

[Website](https://roamwyld.app) · [Portfolio](https://www.kirstenmoberly.com) · [LinkedIn](https://www.linkedin.com/in/kirsten-evans27) · [GitHub](https://github.com/kurrs10)
