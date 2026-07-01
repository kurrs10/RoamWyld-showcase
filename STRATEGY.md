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

### Competitor Matrix (Updated June 26, 2026 — Post Deep Research)

| Feature | Roam Wyld | Tripsy Pro | TripIt Pro | CheckMyTrip | Mindtrip |
|---|---|---|---|---|---|
| Pricing | $4.99/mo · $29.99/yr | $9.99/mo · $59.99/yr | $48.99/yr | Free | Free |
| App Store rating | Target 4.5+ | 4.7★ (4,766 ratings) | 4.8★ (298K ratings) | ~4.0★ | New |
| Gmail import (OAuth, no forwarding) | Yes | No (forward-to only) | Yes (auto-scan) | No | No |
| Offline — full itinerary | Yes | Yes | Partial | Partial | No |
| Visa / entry requirements | Yes (50+ countries, offline) | No | Yes (260+ countries, Pro, online only) | No | No |
| Per-passport logic | Yes | No | Yes (Pro) | No | No |
| Offline visa/entry info | **Yes — unique** | N/A | **No — online only** | N/A | N/A |
| Schengen 90/180 tracker | **Yes — unique** | No | No | No | No |
| Live booking validation | **Yes — unique** | No | No | No | No |
| Emergency contacts (offline) | Yes | No | Partial (embassy) | No | No |
| Language basics (offline) | Yes | No | No | No | Camera only |
| Insurance storage | Yes | No | No | No | No |
| Currency converter (offline) | Yes | No | Rates online only | No | No |
| Real-time flight alerts | **No — gap** | Yes (Pro) | Yes (Pro) | Yes (free) | No |
| Expense tracking | No | Yes (Pro) | No | No | No |
| Weather forecast | No | Yes (10-day, Pro) | No | No | No |
| Trip cover photos / themes | No | Yes — best in class | No | No | No |
| Apple Watch app | No | Yes | No | No | No |
| AI itinerary planning | No | No | No | No | Yes |
| Collaborative group planning | No | Yes (Pro) | Yes | No | Yes |
| Web / desktop interface | No | No | Yes | Yes | Yes |
| Android | No | No | Yes | Yes | No (in dev) |

### What Roam Wyld Wins On (No Competitor Matches)

1. **Schengen 90/180 tracker** — zero competitors have it; digital nomads and long-stay EU travelers have no app solution today
2. **Offline visa + entry requirements** — TripIt has visa info but requires internet; at a border with no data, Roam Wyld works, TripIt shows a loading spinner
3. **Live booking validation** — B2B fraud tech brought to consumers; no consumer app validates flight numbers against AviationStack
4. **Price vs. value** — $29.99/yr vs. Tripsy $59.99/yr with a stronger feature set for international travelers

### Where Roam Wyld Has Gaps (v1.1 Targets)

1. **Real-time flight alerts** — TripIt, Tripsy, and CheckMyTrip all have this; it's the #1 reason users run a second app alongside Roam Wyld; **highest priority v1.1 feature**
2. **Trip cover photos / aesthetic design** — Tripsy's cover photos create emotional pre-trip engagement; users open it just to look at their trip; Roam Wyld needs this to compete on delight
3. **Expense tracking** — Tripsy's most-praised non-core feature
4. **No web client** — limits desktop trip planning; medium-term gap

### Messaging Strategy: Own the Unmet Needs

These pain points appear across competitor reviews. Roam Wyld solves all of them — but must say so explicitly:

| User pain point | Roam Wyld answer | Where to say it |
|---|---|---|
| "App gave me wrong info, almost missed my flight" | Booking validation against live data | App Store subtitle, onboarding |
| "Useless without data" | Works when you land, even without a SIM card | Hero message, App Store screenshots |
| "I use 3 apps for one trip" | One app for the whole international trip | Positioning tagline |
| "Schengen rules terrify me" | Schengen tracker — days of freedom remaining | Feature section, Reddit/community |
| "Customer support doesn't exist" (TripIt complaint) | Respond to every early review personally | Execution, not copy |

### Tripsy Aesthetic Gap — Action Required

Tripsy's UI creates emotional pre-trip engagement through trip cover photos, card layouts, and visual hierarchy. This is their primary acquisition driver and photographs beautifully for App Store screenshots.

**What Roam Wyld needs (v1.1):**
- Trip cover photo per trip (user-selectable or auto from destination)
- Entry requirements presented as "You're clear to go" (green confidence signal, not a compliance checklist)
- Schengen tracker framed as "days of freedom remaining," not days counted against you
- App Store screenshots leading with aspirational trip view, safety features shown in context of real trips

### Pre-Launch Competitive Verification Checklist

Before finalizing App Store copy, verify live:
- [x] Tripsy, TripIt Pro, CheckMyTrip, Mindtrip — deep competitive teardown completed June 26, 2026
- [ ] Any new AI travel companion apps launched since June 2026
- [ ] Apple's iOS travel feature expansion in latest iOS release

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

**Current pricing: $4.99/month · $29.99/year** (set at App Store submission, June 25, 2026)

- Annual plan is the headline offer
- 14-day free trial on both plans
- Market context: Tripsy Pro $59.99/yr, TripIt Pro $48.99/yr — Roam Wyld undercuts both despite stronger feature set for international travelers

**Pricing evaluation schedule (do NOT change pricing before completing this data review):**

| Checkpoint | What to evaluate | Potential action |
|---|---|---|
| 30 days post-launch | Trial-to-paid conversion rate vs. 48.7% travel app benchmark | Hold or adjust |
| 60 days post-launch | Annual vs. monthly split (target: 60%+ annual) | Hold or adjust |
| 90 days post-launch | Churn rate, LTV estimate, review sentiment on price | Consider raise to $39.99/yr |
| 120 days post-launch | Full cohort data — paid CAC vs. LTV if any paid acquisition tested | Final pricing decision |

**When to raise to $39.99/yr:** Research shows $29.99/yr is below market floor vs. competitors. However, raise only after 90+ days of conversion data. If trial-to-paid is above 40% and churn is below 5%/month, a raise to $39.99 is justified and keeps Roam Wyld $20 below Tripsy while capturing more value.

**Free trial:** 14 days on annual. Travel apps convert at 48.7% median trial-to-paid — the product just needs enough time to show value across a planning cycle.

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

## Go-To-Market Strategy (Post-Launch)

### First 90 Days — Organic Only

**Do NOT run paid acquisition in the first 90 days.** At $29.99/yr, industry CAC of $30–200/subscriber makes paid channels break-even to negative LTV. All budget goes to organic until pricing is validated.

### Priority Channel Ranking

**1. Setapp Distribution (apply Week 1)**
Setapp is a Mac/iOS app subscription bundle — users pay $9.99/month and get access to 240+ apps. Tripsy is already on it. Accepted apps get passive discovery from 300K+ power users who are already subscription-comfortable and Apple-native. No CAC. Apply at setapp.com/developers. Acceptance typically takes 2–4 weeks.

**2. Reddit Communities (authentic, not promotional)**
Target: r/solotravel (7M members), r/digitalnomad (2M members), r/travel (7M members).
- Do NOT post "check out my app" — this gets removed and damages reputation
- Reply to existing threads where people ask about Schengen tracking, trip organization, entry requirements
- Provide genuine value first; mention the app naturally when directly relevant
- A single helpful reply in r/solotravel can drive 50–200 installs if it surfaces in Google search results (Reddit ranks highly)
- See Reddit post templates below

**3. Apple Editorial Pitch**
Apple's App Store editorial team features apps in "Apps We Love," "Best New Apps," and curated collections. You can pitch them directly via App Store Connect → App Information → App Store Promotion. The angle: Roam Wyld is the only iOS app with a Schengen tracker at the moment EES (EU Entry/Exit System) went live in April 2026, making compliance tracking newly critical for all non-EU travelers to Europe. This is a news hook Apple editors recognize. Keep the pitch to 2–3 sentences and one compelling screenshot.

**4. Content SEO — Schengen Landing Pages**
Publish 3–5 pages on roamwyld.app targeting searches users make while researching the exact problem Roam Wyld solves. Google indexes these; they drive installs 6–12 months after publishing. Start with:
- "Schengen 90/180 day rule explained" — high search volume, low competition
- "How to track Schengen days" — intent matches exactly what the app does
- "Entry requirements for [Japan/Thailand/Indonesia]" — destination-specific, matches traveler research phase
- "How to organize a multi-country Europe trip" — top of funnel

You don't need a blog platform — simple HTML pages on roamwyld.app with good meta descriptions are enough to index. 500–800 words per page.

**5. Summer Launch Messaging (July 2026)**
The app launches while people are actively traveling, not planning. Adjust all messaging to:
- "Works when you land, even without a SIM card" (not "plan your next trip")
- "Check your entry requirements right now, for free" (immediate utility)
- "Your Schengen days, calculated instantly" (solves an active problem)
January 2027 is the highest-intent planning window — build community and content now so you're established when that surge hits.

### Reddit Post Templates

**Template 1 — Reply to "how do I track Schengen days" threads:**
> The 90/180 rule is genuinely confusing because the window rolls daily, not by calendar month. I've been using Roam Wyld (free iOS app) which has a Schengen tracker built in — you input your entry/exit dates and it shows exactly how many days you have remaining. Saves me from running the math in my head every time I cross a border. [No affiliation, just find it useful]

**Template 2 — Reply to "what app do you use for multi-country trips" threads:**
> Depends what you need. For pure itinerary organization Tripsy looks beautiful. But for international trips where I actually need to know visa requirements and entry rules for each country, I switched to Roam Wyld — it has per-passport entry requirements cached offline so they work without data, plus a Schengen tracker. It's free for the core stuff. Not perfect but fills a gap the prettier apps don't touch.

**Template 3 — Reply to "app that works offline in remote areas" threads:**
> Roam Wyld is offline-first — your itinerary, entry requirements, emergency contacts, and currency data are all cached locally. I tested it in airplane mode before a Japan trip and everything was there. The Gmail import is the Pro feature but the offline stuff is free.

**Note on Reddit posting:** Use your personal account, not a brand account. Post from your genuine experience as a traveler who built this for her honeymoon — that's authentic and the r/solotravel community responds well to founder stories. Don't post more than once per week per subreddit or you'll trigger spam filters.

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
