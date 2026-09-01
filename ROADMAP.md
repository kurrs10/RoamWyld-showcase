# Roam Wyld — Product Roadmap

**Status:** Live on the App Store — v1.3 (Build 30) shipped August 2026

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
| 779-test automated test suite | ✅ Shipped — updated 2026-08-02 (was 699 at launch; count only goes up) |

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

### v1.3 — Navigation, Import & Discovery Improvements (August 2026)
Shipped from a customer-feedback triage: a beta tester's real usage plus the founder's own field notes from a trip.

| Feature | Detail |
|---------|--------|
| Navigation-audit batch | Emergency Info, Phrases, and Currency now all default to the traveler's *current* destination on multi-destination trips instead of always the first — the Emergency Info fix was safety-relevant (wrong-country emergency numbers). Plus: visa/passport alerts auto-expand, trip list sorts by relevance not insert order, day headers show city/country, invite acceptance routes directly into the joined trip. |
| PDF / photo itinerary import | Import a booking confirmation from a PDF or up to 8 photos, alongside the existing Gmail import — same AI parsing pipeline, no separate OCR step. Reviewed by an architecture role and a product-requirements role before build started (see PRODUCT-DECISIONS.md); QA caught and fixed 2 launch-blocking issues before shipping. |
| Phrase translation | One-shot translation tool on the Phrases card, for any phrase and any language — not limited to the ~15 countries the static phrasebook covers. Deliberately kept separate from the still-undecided in-app chat concept. |
| Trip wishlist | Bookmark Discover suggestions to a trip, schedule them into a real booking later, or remove them. |
| Discover — reservation/permit badges | Suggestions that need advance booking or a permit are now flagged. |
| Discover — distance scoping | Suggestions stay within walking/short-transit distance of the destination. |
| Trip timeline — free-time breakdown | Shows the gap between two scheduled bookings on the same day. |
| Hiking / hut-to-hut persona | Discover now surfaces trail systems and multi-day hut-to-hut routes as legitimate suggestions, not just restaurants and city sights — see PRODUCT-DECISIONS.md. |

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

### Itinerary Sophistication (added 2026-08-25)

*Currently scoped as the next active build phase, alongside the widget/cover-photos work below — pushed ahead of push notifications and monetization for now, since neither's trigger conditions have been hit yet.*

| Feature | Priority | Detail |
|---------|----------|--------|
| Chronologically-sorted, gap-aware day view | High | A day's bookings render in actual time order with clear free-time gaps, instead of the order they were added in |
| Basic scheduling-conflict detection | High | Flags when two bookings on the same day genuinely overlap in time, with a shortcut into editing either one |
| Layover awareness improvements | Medium | Detects overnight/cross-midnight connections and manually-entered connecting flights, not just AI-imported same-day ones |

Requirements were scoped down to a buildable spec, with a short list of founder decisions still open before build starts (see DEVLOG).

---

### Held — Pending Founder Decision

| Feature | Status | Detail |
|---------|--------|--------|
| Map view — see all bookings located relative to each other | Held (updated 2026-08-02) | Fully spec'd, but genuinely needs a cost decision first: a paid geocoding provider and a new `address` field on bookings that doesn't exist today. **True offline map tiles are descoped** — judged not valuable enough to customers to justify the cost, and in tension with the app's "everything works offline" positioning if done only partially. |
| In-app trip chat | Parked indefinitely (updated 2026-08-02) | The real open question — an AI assistant vs. peer-to-peer messaging between travelers — needs more user feedback before it can be scoped responsibly. Not blocked on anything else; genuinely undetermined. |

---

### v1.4 — Pro Monetization Launch (Q4 2026)
*Pricing and gate scope finalized 2026-08-31 after a competitive teardown of the travel-app category.*

| Decision | Detail |
|----------|--------|
| Pricing | **$1.99/month · $9.99/year, 14-day free trial, no launch promo.** Deliberately priced well below the category (competitors run $39.99–$59.99/yr) to prioritize downloads, reviews, and word of mouth over early revenue; a measured price increase follows once there's a user base, affecting new subscribers only. |
| Pro gate | Unlimited AI: unlimited Gmail/PDF imports, unlimited transit-direction generations, unlimited Discover, and the planned AI Travel Agent. Free covers a complete trip — **unlimited trips**, all safety/offline features, 10–15 imports per trip, and full transit directions on the first trip. Trip count is not gated. |
| Founding cohort | Every account created on or before the cutoff (2026-08-31) gets **every feature free forever, unconditionally** — including future AI features — with no paywall or upsell ever, and an in-app "you're a founder" acknowledgment. |
| Status | Preparation work (purchase screen, the grandfathering mechanism, an offline-access fix) is code-complete; the purchase gate itself is not live yet — deliberately not bundled into the current build. |

**Gate philosophy:** at a near-impulse price the paid tier is aligned to real per-use cost (the Claude API calls behind import and AI features), not to friction. Everything safety-critical, offline, or already-owned stays free permanently.

---

### Email-forwarding import (designed 2026-09-01, ~3 build sessions)

Customers forward booking confirmation emails to a per-trip, per-person address; a server function parses them with the same AI extraction the app already uses, and stages the result for the user's review on next app open. This is the category-standard import mechanism — TripIt, Tripsy, Wanderlog and KAYAK all work this way — and it means booking import no longer depends on a third-party OAuth verification process. Works identically on iOS and Android (no per-platform OAuth client). Design + provider evaluation (Resend, inbound-only) went through an engineer + architect review; the auth model is a per-person capability token, not sender-address matching, and nothing auto-adds — every forwarded import passes through the user's review step first.

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
| Android | **In progress** (updated 2026-08-25) | React Native codebase is cross-platform. Package scaffolding and build profiles are in place; the business registration needed for an organization Google Play account (no closed-testing gate) has now cleared, unblocking the next concrete steps: registering the Play Console account and a first real Android build attempt |
| Outlook import | 2027 | Gmail covers the core persona; Outlook targets enterprise users — post-traction |
| Affiliate revenue | Ongoing | Airalo (eSIM), SafetyWing (insurance), Wise (currency), iVisa (visa assistance) |

---

## Monetization Timeline

| Milestone | Trigger |
|-----------|---------|
| Monitor usage patterns | v1.0 launch → 30 days |
| Evaluate trial-to-paid conversion | 30/60/90/120-day checkpoints |
| Pro gate activation | When founding cohort data supports pricing confidence |
| First price increase | $9.99/yr → ~$19.99/yr once there's a user base and 90-day conversion data — new subscribers only; existing and grandfathered users unaffected |

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
