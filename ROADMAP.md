# Roam Wyld — Build Roadmap

**Target:** Working app on TestFlight by June 30, 2026 (~7 weeks from May 13)

---

## Product Decisions (locked)

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Pricing | $4.99/mo · $29.99/yr | Competitive with travel apps; ~50% annual discount |
| Free trial | 14 days (both plans) | Users need time to plan a trip AND use Pro mid-travel |
| Entry requirements | **Free** (no paywall) | Safety-critical info should never be paywalled; acquisition hook; no major competitor paywalls this |
| Pro features | AI transit directions + Gmail import | These are the differentiators; everything else is free |
| Domain | roamwyld.app | Purchased June 5, 2026; roamwyld.com was taken |
| Support email | support@roamwyld.app | Forwarded to personal Gmail via Namecheap |
| Website | roamwyld.app (GitHub Pages) | Live with HTTPS as of June 5, 2026 |

---

## Feature Prioritization

Features are scored on two axes: **traveler value** (how much it matters to the user experience) and **build difficulty** (engineering complexity, third-party dependencies, offline requirements).

| Feature | Traveler Value | Build Difficulty | Phase |
|---------|---------------|-----------------|-------|
| Manual trip entry | Critical | Low | 1 |
| Trip timeline view | Critical | Low | 1 |
| User auth + traveler profiles | Critical | Low | 1 |
| Supabase data model | Critical | Low | 1 |
| Email parsing (Gmail/Outlook) | High | Medium | 2 |
| PDF upload + parsing | High | Medium | 2 |
| Booking validation — flights | High | Medium | 2 |
| Booking validation — hotels | High | Medium | 2 |
| Round trip flight toggle | Medium | Low | 2 |
| Step-by-step transit directions | Critical | High | 3 |
| Entry requirements (per passport) | Critical | Medium | 3 |
| AI free-time suggestions (cached) | High | Medium | 3 |
| Offline caching architecture | Critical | High | 3 |
| Group / couple mode | High | High | 3 |
| Both / Split / Free time modes | High | Medium | 3 |
| Customizable alerts | High | Medium | 3 |
| Travel insurance storage | Medium | Low | 4 |
| Mobile service recommendations | Medium | Low | 4 |
| Emergency info (offline) | Medium | Low | 4 |
| Currency + tipping guide (offline) | Medium | Low | 4 |
| Language basics (offline) | Medium | Low | 4 |
| Expense splitting | Medium | Medium | 4 |
| Packing list (AI-generated) | Medium | Medium | v1.1 |
| Weather (cached) | Low | Low | v1.1 |
| Visa tracker | Medium | Medium | 4 |
| Analytics (PostHog) | Internal | Low | 5 |
| Error monitoring (Sentry) | Internal | Low | 5 |
| Beta user access (free Pro for 5-10 testers) | Internal | Low | 5 |
| Group invite viral loop | High | Low | 5 |
| TestFlight beta | Launch | Low | 5 |
| App Store submission | Launch | Medium | 5 |

---

## Phase 1 — Foundation (Week 1: May 13–19)

**Goal:** A working app shell with navigation and the ability to manually create a trip.

Everything built in Phase 1 is the skeleton every future feature attaches to. Getting this right — especially the data model — prevents expensive rewrites later.

### What we build
- Expo + React Native project scaffold (iOS target)
- Bottom tab navigation: Today, Trip, Discover, Profile
- Supabase project setup: auth, database schema, row-level security
- User account creation + login
- Traveler profile (name, passport nationality, travel insurance reference)
- Manual trip creation: add flights (with round trip toggle), hotels, and activities
- Basic trip timeline view: day-by-day list of bookings with confirmation numbers

### Definition of done
- Can create an account, add a trip manually, and view it in a timeline
- Data persists in Supabase across sessions
- App runs on a physical iPhone via Expo Go

---

## Phase 2 — Smart Import + Validation (Weeks 2–3: May 20 – June 2)

**Goal:** Make getting trip data into the app fast and trustworthy.

This is the first real differentiator. Competitors do email import; none do booking validation. Validation is what makes users trust the app with their actual trip.

### What we build
- Email import: connect Gmail / Outlook, auto-detect travel confirmations, deselect individual items before importing
- ~~PDF upload: drag-and-drop zone, parse flights / hotels / activities from e-tickets and agency docs~~ **Deferred to v1.1 (May 27) — email import covers most users; PDF complexity not worth the Phase 2 timeline risk**
- Combine methods: email import + manual entry coexist in one trip
- Booking validation:
  - Flights: verify confirmation number format + check against AviationStack API
  - Hotels: verify property exists via Google Places API; flag unverified bookings
- Validation UI: verified badge, unverified badge, and clear error states
- Cannot advance with zero bookings or missing required fields
- Edit / remove any booking at any time

### Definition of done
- Can forward a real confirmation email and have the booking appear correctly
- Fake flight confirmation numbers are rejected with a clear error
- Mixed import (email + manual) works in one trip

---

## Phase 3 — The Differentiators (Weeks 3–4: June 3–16)

**Goal:** Build the features that no competitor has. This is what makes Roam Wyld worth keeping.

This is the hardest phase technically but the highest value for the trip experience. Offline architecture must be designed here — retrofitting it later is painful.

### What we build
- **Step-by-step transit directions:** User-triggered batch generation via Claude API — one call covers all travel segments for the trip; cached to expo-sqlite for offline use. A "Get Directions" button appears on the trip screen once a travel segment exists. After generation, shows a timestamp. Feature is optional — users who don't tap it get no directions, but the core trip flow is unaffected. No auto-trigger on booking save or itinerary change.
- **Entry requirements:** Static dataset of per-country requirements; per-passport checklist based on traveler nationality; Schengen day tracking; passport validity check; upcoming country alerts
- **Offline caching:** All static content (itinerary, entry requirements, transit directions, AI suggestions) synced to device storage at trip setup; graceful degradation when offline
- **Group / couple mode:** Add a second traveler, shared itinerary view, color-coded by person, pre-trip checklist with per-person assignment
- ~~**Customizable alerts:** Per-category timing (how far in advance) + delivery method (push vs. SMS); alert categories: flights, hotels, activities, restaurants, border crossings~~ **Deferred to post-launch (June 5 decision) — push notification infrastructure + per-category settings is a full week of work; Phase 3 core (transit, entry requirements, offline) is complete and this is not a launch blocker**

> **Deferred to v1.1:** Both/Split/Free Time mode toggle (shared view ships; per-moment toggle post-launch), in-app trip chat

### Definition of done
- Transit directions appear for a travel day and work offline ✓
- Entry requirements checklist is correct for a US and UK passport on the same trip ✓
- ~~Second traveler can be added and the itinerary is shared~~ → **Deferred to Phase 4** (CPO panel May 20: Phase 3 already carrying offline architecture + transit + entry requirements; group mode touches full data model and would have split focus)

---

## Phase 4 — Companion Features (Week 5: June 17–23)

**Goal:** Eliminate every other app the traveler would normally reach for.

These features are individually lower complexity but collectively eliminate Google Translate and a dozen browser tabs. Phase 4 is what takes Roam Wyld from "useful" to "the only app I need."

> **Scope cut per Phase 3 CPO demo panel:** Weather and packing list moved to v1.1 — not moats, native alternatives exist, and the time is better spent on group mode and polish. eSIM/mobile recommendations are lowest priority and will be cut if group mode runs long.

### What we build (priority order)
1. **Emergency info:** Local emergency numbers, nearest hospital reference, embassy contacts per country; fully offline
2. **Currency + tipping:** Offline exchange rate snapshots cached at setup; tipping customs per country
3. **Language basics:** 30–40 key phrases per country with pronunciation; phrases surfaced contextually by itinerary day; fully offline
4. **Group / couple mode:** Add a second traveler, shared itinerary view, color-coded by person — time-boxed to 3 days maximum
5. **Travel insurance:** Policy reference per traveler; key details (emergency claim number, coverage summary) surfaced contextually; available offline
6. **Mobile service recommendations:** eSIM options (Airalo) + carrier international plans per destination; stored offline *(cut if group mode runs long)*
7. **Affiliate integrations (wire into UI):**
   - **Airalo** (eSIM) — ⏳ **Post-launch:** application was rejected (app not yet live); reapply at partners.airalo.com after App Store submission with live app URL; update `AIRALO_AFFILIATE_ID` once approved. Links render now but won't track.
   - **SafetyWing** (travel insurance) — already configured (referenceID 26532413); surface link on insurance storage screen and trip setup
   - **Wise** (currency) — sign up at wise.com/partners; surface link on currency/tipping screen; add `getWiseLink()` to affiliates.ts
   - **iVisa** (visa assistance) — sign up at partners.ivisa.com; surface link on entry requirements screen when a visa is required; add `getIVisaLink(countryCode)` to affiliates.ts

> **Deferred to v1.1:** Expense splitting, visa tracker, weather, packing list

### Definition of done
- Emergency info, currency/tipping, and language basics work in airplane mode
- Group mode: second traveler can be added and the itinerary is shared

---

## Phase 5 — Polish + Launch (Weeks 6–7: June 24–30)

**Goal:** Ship a stable, observable app to TestFlight and submit to the App Store.

### What we build
- PostHog analytics: instrument key events (trip created, import method used, transit directions viewed, suggestion tapped, alert engaged)
- Sentry error monitoring: crash reporting, performance monitoring, observability dashboard
- Storybook: document core UI components
- Unit tests: critical paths (booking validation, offline cache, entry requirements logic)
- App icon, splash screen, App Store screenshots
- **Beta user access system:** `beta_access` Supabase table + `useProAccess()` hook; admin grants free Pro to up to 10 testers by inserting their user_id; no code change needed per tester
- **Group invite viral loop:** When a second traveler is added to a trip, the trip owner sees a shareable invite link; the invited user downloads the app, lands on that trip, and sees a Pro upgrade prompt with the message "Your travel partner uses Roam Wyld Pro — upgrade to unlock transit directions for your shared trip"
- **RevenueCat integration:** Install `react-native-purchases`, configure with App Store product IDs, wire into `useProAccess()` hook to replace the TODO subscription check; handles receipt validation, entitlement management, and webhook events for free until $2.5k MRR
- **App Store subscription + introductory offer setup (must happen before first submission):**
  - Create subscription products in App Store Connect: Monthly ($4.99/mo) and Annual ($29.99/yr) ✓ Done June 5
  - Configure introductory offers: 14-day free trial on both monthly and annual ✓ Done June 5 (rationale: users need time to plan a trip AND use it mid-travel)
  - ⚠️ Introductory offers must be configured before the subscription goes live — cannot be added retroactively to existing subscribers
  - Link RevenueCat to App Store Connect via API key
- **Day-20 trial activation prompt:** If a user is in their free trial and has not created a trip by day 20, surface an in-app prompt: "You have 10 days left in your trial — create your first trip to see Roam Wyld in action"
- **Proprietary LICENSE file:** Add `LICENSE` to repo root declaring all rights reserved; no open-source license
- **JS bundle obfuscation:** Add `react-native-obfuscating-transformer` to scramble variable names in the production bundle
- **Terms of Service:** Include prohibition on reverse engineering in app ToS before App Store submission
- **Form LLC (before App Store submission):** Register a single-member LLC in Wyoming — ~$100 to form, $60/year. File at wyofile.wyo.gov. Takes ~20 min online, no lawyer needed. Use the LLC name + EIN in App Store Connect, RevenueCat, and affiliate agreements (Airalo, SafetyWing). Once formed, update SafetyWing affiliate profile to replace personal SSN with business EIN.
- **⚠️ Trademark Roam Wyld (post-launch, optional at this stage):** File only after app proves traction — ~$350 at uspto.gov, Class 42. Protection begins at filing date.
- **Portfolio showcase repo:** Create public `kurrs10/roam-wyld-showcase` repo with polished README, screenshots, architecture overview, and sanitized code samples — links to this from portfolio site instead of private main repo
- TestFlight build: internal beta for real-world trip trial with beta users
- App Store submission
- **Post-launch: Reapply to Airalo affiliate program** — application was rejected before App Store launch; reapply at partners.airalo.com with live App Store URL → get Impact publisher ID → update `AIRALO_AFFILIATE_ID` in `src/services/affiliates.ts`
- **Gmail OAuth — switch to development build for testing:** `npx expo run:ios` creates a standalone build where the `roam-wyld://` custom URL scheme works natively. Remove the `exp://` redirect URIs from Google Cloud Console after this; add `com.roam-wyld.app` as an iOS OAuth client (bundle ID) if not already present. This permanently resolves the Expo Go redirect URI mismatch and is required before TestFlight anyway.

### Definition of done
- App is installable on iPhone via TestFlight
- RevenueCat entitlement gates all Pro features correctly
- Beta users (by user_id) can access all Pro features without a subscription
- Introductory offer (30-day trial) confirmed active in App Store Connect before submission
- Group invite link works: recipient downloads app, opens shared trip, sees Pro upgrade prompt
- LICENSE file present, ToS includes reverse engineering prohibition
- Trademark application filed at USPTO
- Showcase repo live and linked from portfolio site
- Zero crash-level bugs on the core trip flow
- App Store submission initiated

---

## What Can Slip If We're Behind

If Phase 3 runs long, these are the safe cuts that don't break the core experience:

| Feature | Impact if cut | Re-add in |
|---------|--------------|-----------|
| Expense splitting | Low — Splitwise exists | Post-launch v1.1 |
| Packing list | Low — nice to have | v1.1 (cut from Phase 4 per CPO) |
| Weather | Low — native weather app exists | v1.1 (cut from Phase 4 per CPO) |
| PDF upload | Medium — email import covers most users | Post-launch v1.1 |
| Storybook | Internal only | Post-launch |
| Both/Split/Free time modes | Medium — can default to shared view | Post-launch v1.1 |

**Never cut:** Offline caching, transit directions, entry requirements, booking validation. These are the core promise of the app.

---

## Post-Launch Operations — Upgrade Signals

These are not build tasks. They are business decisions triggered by measurable signals after App Store launch.

### AviationStack — Upgrade to Professional (~$49.99/mo)

**What it unlocks:** Flight schedule data for future bookings. Currently, future flights skip API validation and show no badge. On Professional, users get a real ✓ Verified badge on bookings months before departure — a meaningful trust signal.

**Upgrade when ANY of these are true:**
- MRR hits $50+ (plan cost is covered by ~25 monthly or 4 annual subscribers)
- Support requests mention confusion about unverified future flights
- Validation request volume approaches 100/mo (free tier limit)
- A competitor ships verified future-flight badges and it becomes a differentiator gap

**How to upgrade:** aviationstack.com → upgrade plan → update `EXPO_PUBLIC_AVIATIONSTACK_KEY` in Vercel env vars (production). No code change required — the service already handles schedule data; just the free-tier date guard in `validation.ts` can be removed once on a paid plan.

---

### Google Places — Upgrade or add billing cap

**What it unlocks:** Hotel validation currently works on free tier (200 req/day). If daily active users exceed ~100, you'll hit quota limits and hotel validation silently skips. Add a billing account with a spend cap rather than a plan upgrade.

**Upgrade when:**
- DAUs exceed 100 (Places API free quota is 200 req/day; shared across all users)
- Hotel "could not be verified" support tickets appear
- Dashboard shows quota errors in GCP Console

**How to upgrade:** GCP Console → Billing → link account → set $20–$50/mo spend cap. Cost is ~$0.017/request above free tier — manageable until significant scale.

---

### Claude API — Monitor token spend

**What it unlocks:** Transit directions are generated via Claude API per trip. Cost scales with usage.

**Watch when:**
- MRR > $200 — run a monthly API cost vs. revenue check
- Transit directions are being regenerated frequently (check PostHog events)
- Consider caching at the Supabase layer (already done in SQLite) to reduce repeat calls

---

## Timeline Summary

| Phase | Dates | Focus |
|-------|-------|-------|
| 1 — Foundation | May 13–19 | Scaffold, auth, manual entry, timeline |
| 2 — Import + Validation | May 20 – June 2 | Email/PDF import, booking validation |
| 3 — Differentiators | June 3–16 | Transit, entry requirements, AI, offline, groups, alerts |
| 4 — Companion Features | June 17–23 | Insurance, mobile service, emergency, currency, language, expenses, packing, weather, visa |
| 5 — Polish + Launch | June 24–30 | Analytics, Sentry, testing, TestFlight, App Store |
