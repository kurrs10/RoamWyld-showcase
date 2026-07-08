# Roam Wyld — Product Roadmap

**Status:** Live on the App Store — v1.1 shipped July 2026

---

## What's Shipped

### v1.0 — Launch (June 30, 2026)
The core product. Every feature below is live, free, and works offline.

| Feature | Status |
|---------|--------|
| Manual trip entry (flights, hotels, activities) | ✅ Shipped |
| Gmail booking import (OAuth, AI-parsed) | ✅ Shipped |
| Step-by-step AI transit guidance | ✅ Shipped |
| Visa & entry requirements per destination | ✅ Shipped |
| Offline caching — full itinerary, directions, requirements | ✅ Shipped |
| Booking validation — flights (AviationStack) + hotels (Google Places) | ✅ Shipped |
| Discover — AI suggestions by destination, budget, interests | ✅ Shipped |
| Emergency info — local numbers, embassy contacts, hospitals | ✅ Shipped |
| Currency + tipping guide (offline) | ✅ Shipped |
| Language basics — 30–40 key phrases per country (offline) | ✅ Shipped |
| Travel insurance reference storage | ✅ Shipped |
| Schengen day tracker | ✅ Shipped |
| PostHog analytics + Sentry error monitoring | ✅ Shipped |
| 699-test automated test suite | ✅ Shipped |

**Launch decision:** v1.0 launched fully free. All founding cohort users receive Pro access permanently — no charge, no expiration — when Pro launches in v1.1.

---

### v1.1 — Gmail Import & Coverage Expansion (July 2026)
Shipped one week post-launch based on early user feedback.

| Improvement | Detail |
|------------|--------|
| Gmail import now searches all folders | Custom labels, archives, and subfolders included — previously inbox-only |
| Trash and spam excluded from import | Prevents deleted/junk emails surfacing as bookings |
| Expanded airline coverage | 80+ international carriers added including TransNusa, Peach, regional Asia-Pacific airlines |
| User-compiled itinerary emails now parsed | Emails like "Honeymoon Itinerary — Full Details" extract every booking as individual entries |
| Snippet limit increased 4,000 → 8,000 chars | Long itinerary emails no longer truncated mid-trip |
| Booking import scoring improved | Itinerary-style emails surface at top of import regardless of sender domain |

---

## What's Next

### v1.2 — Alerts & Notifications (Q3 2026)
*Top priority. Real-time flight status is the #1 reason users run TripIt or CheckMyTrip alongside Roam Wyld.*

| Feature | Priority | Detail |
|---------|----------|--------|
| Push notifications — Firebase FCM | Prerequisite | Required infrastructure for all alerts |
| Real-time flight alerts | TOP | Gate changes, delays, cancellations surfaced before the traveler checks |
| Trip reminders | High | "Your flight to Tokyo is tomorrow" day-before nudge |
| Re-engagement nudges | Medium | Prompt users to finish itinerary setup before departure |

---

### v1.3 — Group & Couple Mode (Q3 2026)
*Primary viral acquisition loop — every shared trip is a potential new install.*

| Feature | Priority | Detail |
|---------|----------|--------|
| Add a travel partner to any trip | High | Shared itinerary view, color-coded by person |
| Group invite link | High | Shareable link → recipient downloads app → lands on shared trip |
| Per-person booking assignment | Medium | Assign specific bookings to each traveler |
| Pre-trip checklist with per-person tasks | Medium | Packing, visa tasks, etc. assigned to each person |

---

### v1.4 — Pro Monetization Launch (Q4 2026)
*All v1.0 founding cohort users permanently grandfathered at no charge.*

| Decision | Detail |
|----------|--------|
| Pro gate | Gmail import only — everything else stays free |
| Pricing | $4.99/mo · $29.99/yr |
| Free trial | 14 days |
| Founding cohort | All users who signed up before Pro launch get Pro forever |
| Trigger | When data shows: 6+ bookings/trip cohort identified, Gmail import rate stable, D30 retention benchmarked |

**Pricing rationale:** Wanderlog charges $39.99/yr for Gmail import. TripIt charges for smart parsing. Roam Wyld gates one feature, stays below both competitors, and lets all companion features (entry requirements, emergency info, offline access) remain free permanently.

---

### v1.5 — Content & Export (Q4 2026)

| Feature | Priority | Detail |
|---------|----------|--------|
| Trip cover photos | High | Per-trip cover image — closes the Tripsy aesthetic gap |
| PDF itinerary export | Medium | Full trip export as a shareable PDF; no competitor has this |
| Per-trip budgeting | Medium | Multi-currency spend tracking per trip |
| Packing list (AI-generated) | Low | Generated from destinations + trip duration |

---

### v2.0 — AI Travel Agent

*The evolution from passive itinerary organizer to active trip intelligence layer.*

Unlike generic AI chatbots, the Roam Wyld travel agent knows your actual trip — your bookings, layover windows, passport nationality, visa situation, and free-time gaps. Every answer is grounded in your real itinerary.

| Capability | What it does |
|---|---|
| **Natural language trip planning** | Describe what you want and the agent builds a day-by-day itinerary you can import directly into your trip |
| **Proactive conflict detection** | Scans bookings for issues — late-night arrivals vs. hotel check-in cutoffs, risky layovers, visa expiry overlapping your stay |
| **Free-time gap filling** | Identifies unscheduled windows between bookings and suggests activities calibrated to your destinations and style |
| **Pre-trip briefing** | Personalized pre-departure checklist per trip: visa requirements, passport validity, currency, power adapters, vaccination advisories — specific to your passport and destinations |
| **Trip Q&A** | Natural language answers about your own trip: "How many Schengen days will I have left when I get to France?" "Do I need a visa for my Dubai layover?" |
| **Disruption re-planning** | When a flight is canceled or delayed, the agent proactively suggests rebooking options and flags downstream bookings now at risk |

**Why this is the right v2.0 move:** Claude is already integrated for transit directions and discover suggestions. The agent is an extension of existing infrastructure. The context advantage is the moat — Roam Wyld knows your trip in a way no generic AI does.

---

### Future — Platform Expansion

| Feature | Timeline | Detail |
|---------|----------|--------|
| Android | 2027 | React Native codebase is cross-platform; iOS-first was a launch scope decision |
| Outlook import | 2027 | Gmail covers the core persona; Outlook targets enterprise users — post-traction |
| PDF itinerary upload | 2027 | Brittle across formats; email import covers 90% of the use case at launch |
| Affiliate revenue | Ongoing | Airalo (eSIM), SafetyWing (insurance), Wise (currency), iVisa (visa assistance) |

---

## Monetization Timeline

| Milestone | Trigger |
|-----------|---------|
| Monitor usage patterns | v1.0 launch → 30 days |
| Evaluate trial-to-paid conversion | 30/60/90/120-day checkpoints |
| Pro gate activation | When founding cohort data supports pricing confidence |
| Pricing review | $29.99/yr → potentially $39.99/yr after 90-day data |

---

## What Will Never Be Cut

These four features are the core product promise. No scope decision touches them:

- **Offline caching** — travelers need this app when they have no signal
- **Transit guidance** — the feature no competitor provides at this depth
- **Entry requirements** — safety-critical; never paywalled
- **Booking validation** — trust is the product; a wrong confirmation number at the airport is a failure

---

## Infrastructure Upgrade Signals

| Service | Upgrade Trigger |
|---------|----------------|
| AviationStack (free → $49.99/mo Professional) | MRR $50+ OR validation requests approach 100/mo free limit |
| Google Places (add billing cap) | DAUs exceed 100 |
| Claude API (cost review) | MRR > $200 — benchmark Gemini Flash for Gmail parsing if cost delta justifies |
