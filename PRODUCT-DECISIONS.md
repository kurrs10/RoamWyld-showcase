# Roam Wyld — Product Decision Log

A record of the significant product decisions made during the build: what was chosen, what was rejected, and why. This is the document that shows how a PM thinks, not just what they shipped.

---

## Architecture Decisions

### Offline-First vs. Sync-on-Demand
**Decision:** Full offline-first — all critical content cached to device at trip setup.
**Rejected:** Background sync with graceful degradation.
**Why:** The highest-anxiety moments in travel (at the airport, underground, abroad on roaming) are exactly when connectivity is least reliable. An app that degrades "gracefully" is an app that fails the user at the worst possible time. Offline-first isn't a nice-to-have for a travel companion — it's the product promise.

### User-Triggered Transit Directions (Not Auto-Generate)
**Decision:** "Get Directions" button — user triggers generation once their itinerary is complete.
**Rejected:** Auto-generate on every booking save.
**Why:** Auto-triggering on save fires before the itinerary is complete, regenerates on every edit, and costs 5–15× more in API calls per trip. Users know when their itinerary is "done enough" — they should control when directions are generated. The feature is also optional; users who don't want AI directions shouldn't have to pay for them implicitly through higher subscription pricing.

### Transit Directions via Backend Proxy (Not Client-Side API Call)
**Decision:** All AI calls routed through a backend Edge Function with JWT verification.
**Rejected:** Direct client-side API call with key in the app bundle.
**Why:** API keys in mobile app bundles are extractable. This isn't a theoretical risk — it's a standard attack. The backend proxy adds one network hop but makes key exposure impossible by architecture, not by policy.

---

## Feature Scope Decisions

### Booking Validation: Blocking vs. Background
**Decision:** Validation blocks save — you cannot save a booking that fails validation.
**Rejected:** Validate in background after save, surface results as a badge update.
**Why:** Background validation creates a false sense of security. A user who saves a flight confirmation, sees "pending," and moves on has no idea their confirmation number is wrong until they're at the airport. Blocking save is more friction up front and zero friction when it matters — at the airport, the confirmed number is confirmed.

### Gmail Import: Skip Validation for Imported Bookings
**Decision:** Bookings from confirmed Gmail imports are trusted; validation status set to "skipped."
**Rejected:** Run AviationStack + Google Places validation on every imported booking.
**Why:** 20+ API calls per import session, many failing on regional carriers and boutique hotels not in the validation databases. More importantly: these bookings came from real confirmation emails — they're already trusted by definition. Validation is for manual entry, where human typos are the actual risk.

### Outlook Import: Deferred to v1.1
**Decision:** Gmail only at launch.
**Rejected:** Build both Gmail and Outlook OAuth for launch.
**Why:** Gmail covers ~35% of email users; the Roam Wyld target persona (self-planned, leisure travel, 30-55) skews heavily Gmail. Outlook adds a second OAuth surface, a second API integration, and targets a different (enterprise) demographic. The incremental coverage doesn't justify the Phase 2 time — and beta feedback will confirm whether demand warrants v1.1 priority.

### PDF Upload: Deferred to v1.1
**Decision:** Email import + manual entry covers launch.
**Rejected:** Build PDF parsing for Phase 2.
**Why:** PDF parsing is format-dependent, brittle, and has a high error rate across the variety of agency docs and e-tickets travelers actually use. Email import covers the high-confidence confirmation email case. PDF is the long tail — valuable, but not a launch blocker.

---

## UX Decisions

### Entry Requirements: Suppressed Schengen Counter for Mixed Trips
**Decision:** Schengen day counter only displays when all recognized trip destinations are Schengen members.
**Rejected:** Show a Schengen counter for any trip that includes at least one Schengen country.
**Why:** In a mixed trip (France + Japan), hotel nights can't be reliably attributed to specific countries. A count that's wrong is worse than no count — a traveler who trusts an inflated Schengen day count could unknowingly violate the 90/180-day rule. Individual country cards still show correct visa rules. Showing nothing is better than showing wrong.

### Destination Input: Free Text (Not Structured Country Picker)
**Decision:** Tag-based free text input for destinations at launch.
**Rejected:** Structured country picker with ISO code binding.
**Why:** Free text is faster to build and easier to use for planning. The downside — entry requirements lookup requires country-level resolution, which free text doesn't guarantee — is addressed in Phase 3 with a graceful "No data" card for unrecognized inputs. The structured picker is the right long-term answer; it's a Phase 4 polish item, not a launch blocker.

### Validation Badge: Amber for "Verified"
**Decision:** Shipped amber as the verified badge color.
**Flagged:** UX research panel noted amber communicates "check this" to most users, not "approved."
**Status:** Logged as a Phase 4 polish item. The correct fix is green for verified, amber for pending. Didn't fix in Phase 3 because color changes require careful contrast re-testing across all badge states.

---

## Scope / Prioritization Decisions

### Weather and Packing List: Cut to v1.1
**Decision:** Both features moved out of Phase 4 per CPO demo panel recommendation.
**Why:** Neither is a moat. Native iOS weather is good enough; Apple Weather ships on every iPhone. AI packing lists exist in multiple free apps. Neither feature would prevent a user from choosing Roam Wyld over a competitor. Time in Phase 4 is better spent on group mode, emergency info, and language basics — features with no free native alternative.

### Group Mode: 3-Day Timebox in Phase 4
**Decision:** Group mode ships in Phase 4 with a strict 3-day development window.
**Rationale:** Group mode is the primary viral acquisition loop — every shared trip is a free install event. It's worth the complexity. But it touches the entire data model, and scope creep risk is high. If it can't ship cleanly in 3 days, it moves to v1.1 to protect the Phase 5 launch window. The June 30 deadline is non-negotiable.

### App Store Submission: June 27 Internal Target (Not June 30)
**Decision:** Internal target for App Store submission is June 27.
**Why:** Apple review typically takes 24–48 hours for a first submission. Three days are not slack — they are the review window. Submitting on June 30 means the app is likely live July 2–3. The CPO panel flagged this explicitly: June 27 is the deadline, not June 30.

---

## Process Decisions

### Demo Panels at the End of Every Phase
**Decision:** Every phase ends with a structured review by six disciplines before it closes.
**Why:** A solo PM-led build has no built-in check on blind spots. No one is reviewing the architecture for scalability risks, no one is catching UX trust issues, no one is doing QA-level edge case analysis. The demo panel creates cross-functional accountability. It also creates a record — the DEVLOG documents not just what was built but what everyone thought about it. That's the difference between shipping fast and shipping blind.

### Never Close a Phase Without a DEVLOG Entry
**Decision:** No phase is marked done without a written record of decisions, problems solved, and panel feedback.
**Why:** The code shows what was built. The DEVLOG shows how decisions were made. For a solo project, this is the equivalent of a product council decision memo — it forces reflection and creates an artifact that explains the reasoning behind every significant choice.
