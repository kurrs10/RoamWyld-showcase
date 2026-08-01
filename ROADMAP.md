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
| Step-by-step AI transit guidance | ⚪ Descoped — built (`transit-directions` Edge Function + service layer, still in the codebase) but the UI that surfaced it lived on an old screen that was never wired into navigation after a later screen replaced it. Not reachable in the live app. |
| Visa & entry requirements per destination | ✅ Shipped |
| Offline caching — full itinerary, requirements | ✅ Shipped |
| Booking validation — flights (AviationStack) + hotels (Google Places) | ✅ Shipped |
| Discover — AI suggestions by destination, budget, interests | ✅ Shipped |
| Emergency info — local numbers, embassy contacts, hospitals | ✅ Shipped |
| Currency + tipping guide (offline) | ✅ Shipped |
| Language basics — 30–40 key phrases per country (offline) | ✅ Shipped |
| Travel insurance reference storage | ✅ Shipped |
| Schengen day tracker | ✅ Shipped |
| Group / couple mode — add a travel partner, shared itinerary, invite link | ✅ Shipped (Phase 4, pre-launch) — was misfiled under "What's Next" as a future v1.3 item below; corrected 2026-08-01. Shared-trip read access for accepted invitees expanded in Build 20. |
| PostHog analytics + Sentry error monitoring | ✅ Shipped |
| 759-test automated test suite | ✅ Shipped — updated 2026-08-01 (was 699 at launch; count only goes up) |

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

> **Note on version labels below (added 2026-08-01):** the v1.2/v1.3/v1.4 labels in this section were assigned aspirationally, before actual releases happened, and have since drifted from what those version numbers really shipped (real v1.2, Build 20, shipped shared-trip invites + expanded Gmail import — not what's labeled v1.2 below). Read these as ordered future work, not as commitments to specific version numbers.

### Alerts & Notifications
*Real-time flight status is the #1 reason users run TripIt or CheckMyTrip alongside Roam Wyld — but this is currently paused, not top priority.*

**🛑 Status update 2026-08-01: no-go for now.** Foundational app-experience work takes priority over building this out. The interim approach — computing flight/layover duration from Gmail-parsed confirmation text plus a calculated fallback — is judged sufficient for the time being. Revisit once foundational work is further along.

| Feature | Priority | Detail |
|---------|----------|--------|
| Push notifications — Firebase FCM | Prerequisite (paused) | Required infrastructure for all alerts |
| Real-time flight alerts | Paused | Gate changes, delays, cancellations surfaced before the traveler checks |
| Trip reminders | High (paused) | "Your flight to Tokyo is tomorrow" day-before nudge |
| Re-engagement nudges | Medium (paused) | Prompt users to finish itinerary setup before departure |

---

### Group & Couple Mode Extensions

Core group/couple mode already shipped (see What's Shipped above) — this is further extension work, not the base feature.

| Feature | Priority | Detail |
|---------|----------|--------|
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

### v2.0 — AI Travel Agent + Professional Agent Platform

Two complementary capabilities: an AI-powered personal travel agent for consumers, and a B2B portal that lets real travel agents build and deliver itineraries directly to travelers' Roam Wyld apps.

#### Part 1 — AI Personal Travel Agent (Consumer)

The AI agent transforms Roam Wyld from a passive itinerary organizer into an active trip intelligence layer. Unlike generic AI chatbots, this agent knows your actual trip — your bookings, dates, layover windows, passport nationality, and visa situation.

| Capability | What it does |
|---|---|
| **Natural language trip planning** | Describe what you want and the agent builds a full day-by-day itinerary importable directly into your trip |
| **Proactive conflict detection** | Late-night arrivals vs. hotel cutoffs, risky layovers, visa expiry overlapping your stay — caught before you travel |
| **Free-time gap filling** | Identifies unscheduled windows and suggests activities calibrated to your destinations and style |
| **Pre-trip briefing** | Personalized checklist: visa requirements, passport validity, currency, adapters, vaccination advisories — specific to your passport and destinations |
| **Trip Q&A** | "How many Schengen days will I have left when I get to France?" "Do I need a visa for my Dubai layover?" Answered against your actual itinerary |
| **Disruption re-planning** | Flight canceled? Agent surfaces rebooking options and flags downstream bookings now at risk |

#### Part 2 — Professional Travel Agent Platform (B2B)

Travel agents build itineraries for clients and deliver them directly to travelers' Roam Wyld apps — ready to use offline, with all companion features available from day one.

**The problem:** Travel agents send PDFs. Clients can't use them offline, don't get alerts, and re-enter everything manually if they want a travel app. Roam Wyld becomes the delivery format.

| Feature | Detail |
|---|---|
| **Agent web portal** | `agents.roamwyld.app` — build full itineraries with the same booking types as the app |
| **Multi-traveler assignment** | Assign bookings to individual travelers or the full group |
| **PDF + paste import** | Import from existing tools; Claude parses and structures the bookings |
| **One-tap delivery** | Traveler opens a link → full trip is waiting in their app |
| **Read-only traveler view** | Agent owns the source of truth; travelers view but can't edit |
| **Branded delivery** | Professional tier: "Your trip from [Agency Name] is ready" |
| **Traveler analytics** | Did they open it? Did they enable offline? Agent gets confirmation |

**Agent pricing:** Free (3 active trips) · Professional $29/mo (unlimited, branded) · Enterprise (white-label)

**Why this wins:** No competitor does this. TripIt, Tripsy, and Wanderlog are consumer-only. Every agent-delivered trip is a new Roam Wyld install — the traveler becomes an organic user for their next trip. Agent subscriptions are higher-value and lower-churn than consumer Pro.

---

### Future — Platform Expansion

| Feature | Timeline | Detail |
|---------|----------|--------|
| Android | **In progress, targeted to begin mid-August 2026** (updated 2026-08-01) | React Native codebase is cross-platform. Work begins after the current iOS feature-update pass finishes; targeting Google Play (org account, no closed-testing gate) |
| Outlook import | 2027 | Gmail covers the core persona; Outlook targets enterprise users — post-traction |
| PDF / photo itinerary upload | **Actively spec'd, build pending founder review** (updated 2026-08-01) | Full interaction spec complete: unified action sheet for PDF or photo capture, Claude vision for photos (~1.5–2¢/import), 8-page cap. No longer a 2027 idea — this is near-term backlog. |
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
- **Transit guidance** — the feature no competitor provides at this depth. ⚠️ **Currently contradicted by reality** (see What's Shipped above — this is descoped/unreachable in the live app as of 2026-08-01). Leaving this principle in place as a statement of intent, but it needs either a fix (re-wire the existing built feature into the live screen) or an honest scope decision, not silence.
- **Entry requirements** — safety-critical; never paywalled
- **Booking validation** — trust is the product; a wrong confirmation number at the airport is a failure

---

## Infrastructure Upgrade Signals

| Service | Upgrade Trigger |
|---------|----------------|
| AviationStack (free → $49.99/mo **Basic**, corrected 2026-08-01 — "Professional" is a separate, pricier $149.99/mo tier) | MRR $50+ OR validation requests approach 100/mo free limit |
| Google Places (add billing cap) | DAUs exceed 100 |
| Claude API (cost review) | MRR > $200 — benchmark Gemini Flash for Gmail parsing if cost delta justifies |
