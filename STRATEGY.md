# Roam Wyld — Product Strategy

A summary of the competitive positioning, monetization model, and key strategic decisions behind Roam Wyld.

---

## The Market Gap

No single travel app currently combines:
1. Booking validation against live airline and hotel data
2. Per-passport entry requirements with mixed-nationality Schengen tracking
3. Step-by-step transit directions tied to the actual itinerary, cached offline
4. Full offline parity across all critical content

Individually, partial versions exist — Rome2Rio for routing, Sherpa for visa info, TripIt for email import. None are integrated into a companion that knows the user's specific bookings. None work offline without a separate download step. None handle mixed-nationality groups.

---

## Competitive Positioning

| Feature | TripIt Pro | Wanderlog Plus | Any AI Itinerary App | Roam Wyld |
|---------|-----------|---------------|---------------------|--------|
| Booking validation | None | None | None | Yes |
| Per-passport entry requirements | None | None | None | Yes |
| Step-by-step transit (offline) | None | None | None | Yes |
| Full offline parity | Partial | Partial (paywall) | None | Yes |
| Group mode | Basic | Strong | None | Yes |
| Pricing | $49/yr | ~$30/yr | Free/varies | $14.99/yr (launch) |

**Key insight:** The competitive risk isn't that a competitor built these features — none have. The risk is execution quality, specifically transit direction accuracy in secondary cities. The feature needs to be better than Google Maps for connected transit, which is a high bar.

---

## Monetization

### Tiers

**Free:** 1 active trip, booking validation, entry requirements, emergency info, first transit direction set.

**Pro — $14.99/year (launch) · $1.99/month:**
Unlimited trips, all transit directions, group mode, Schengen alerts, AI suggestions, insurance storage, Airalo eSIM recommendations.

### Pricing Strategy

Launch at $14.99/year to build reviews and trust with early adopters. Raise to $24.99/year after 200 reviews at 4.4+ stars (or 6 months). Tie the second raise to the v1.1 feature release (expense splitting, packing list) for a narrative hook.

Key rule: the first transit direction is always free, on every first trip. That's the conversion driver — users pay after they've experienced the feature, not before.

### What's Never Paywalled

Booking validation, entry requirements, emergency info, the core trip timeline, and basic travel alerts. These are either safety-critical or trust-building — paywalling them would generate legitimate negative reviews and, in the case of emergency info, create real risk for travelers in distress.

### Affiliate Revenue

| Partner | Placement | Commission |
|---------|-----------|-----------|
| Airalo (eSIM) | 3–5 days before each destination, country pre-filtered | 10–18% per purchase |
| SafetyWing (insurance) | Trip setup → traveler profile | $10–25 per policy |
| iVisa | Alongside entry requirements when a visa is required | Per application |
| Wise | Currency feature | Per approved account |

---

## Key Prioritization Decisions

**Never Cut (non-negotiable for v1):**
Offline caching, transit directions, per-passport entry requirements, booking validation. These are Roam Wyld's reason to exist. Everything else can slip.

**Cut to v1.1:**
Weather, packing list, expense splitting, visa tracker, Outlook import, PDF upload. All valuable — none are moats. Native alternatives exist for weather; email import covers the booking import case; expense splitting has Splitwise.

**Group mode: time-boxed to 3 days in Phase 4**
High-value feature and the primary viral acquisition loop (every shared trip is a free install event). But it touches the entire data model — if it can't ship cleanly in 3 days, it moves to v1.1 to protect the Phase 5 launch window.

---

## Risk Register (Top Items)

| Risk | Likelihood | Mitigation |
|------|-----------|-----------|
| Transit direction quality in secondary cities | Medium | Quality-test in 5+ cities before launch; disclaimer visible |
| App Store rejection on first submission | Medium | Full pre-submission checklist; demo account provided; no placeholder content |
| Wanderlog closes the gap before launch | Low-Medium | Verify their current feature set 30 days before submission |
| June 30 deadline — Phase 4 runs long | Medium | Group mode time-boxed; clear cut list if behind |

---

## Success Metrics

| Category | Metric | Target |
|----------|--------|--------|
| Activation | Trip setup completion rate | > 60% |
| Engagement | Daily active use during active trips | > 50% of travel days |
| Engagement | Transit direction usage per travel day | > 40% |
| Retention | Return trip creation within 6 months | > 25% |
| Quality | Crash-free session rate | ≥ 99.5% |
| Quality | Offline feature success rate | ≥ 99% |
| Growth | Group invite → new install | > 15% |
| Business | App Store rating within 90 days | ≥ 4.5 stars |
