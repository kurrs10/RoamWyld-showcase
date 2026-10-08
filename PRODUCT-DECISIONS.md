# Roam Wyld — Product Decision Log

A record of the significant product decisions made during the build: what was chosen, what was rejected, and why. This is the document that shows how a PM thinks, not just what they shipped.

---

## Monetization Decisions

### v1.0 Launch: 100% Free (June 30, 2026)
**Decision:** Launch v1.0 with all features free. No paywall, no in-app purchases, no subscription. `ALL_FREE = true` flag in `useProAccess.ts` grants all users Pro access at launch.
**Rejected:** Launching with a $4.99/mo paywall gating Gmail Import.
**Why — strategic:**
- App Store rejection forced the evaluation (Guideline 2.1(b): Apple couldn't find IAPs; Guideline 3.1.2(c): missing functional links in paywall). Fixing RevenueCat + IAP configuration was possible but introduced risk and delay.
- Zero users = zero revenue risk. Time to live was the most important metric given a deadline of June 30 and the honeymoon use case.
- Gmail Import free in v1.0 is a growth lever, not a giveaway. Users who get hooked during the free period are stronger v1.1 conversion candidates than cold users hitting a paywall.
- Competitive analysis of TripIt, Wanderlog, Roadtrippers, TravelSpend, PackPoint confirmed: manual booking, entry requirements, currency, and emergency info are all expected free. Gmail Import (email auto-scan) is the one feature competitors monetize. Wanderlog charges $39.99/yr for it explicitly.

**Why — user psychology:**
- User research finding: free-to-paid transitions cause user backlash not because of price but because of perceived loss. Travel users are highest-stress during trip planning — introducing a paywall mid-planning cycle is the worst UX moment. Free launch with clear "founding cohort" messaging sets the right expectation for v1.1.
- Users with 6+ bookings per trip are the highest conversion candidates. v1.0 lets those users be identified organically before any paywall appears.

**Metrics to track in v1.0 before Pro launch:**
- Bookings entered per trip (target cohort: 6+)
- Gmail Import tap and usage rate
- Session frequency during active trip planning
- D7, D30, D90 retention

---

### Pro Strategy
**Original decision (June 2026):** gate only Gmail Import behind Pro; one clear feature that demonstrably saves time is more persuasive than a feature list, and competitors already charge for it (Wanderlog $39.99/yr).

**Superseded 2026-08-31, after a competitive teardown of the whole category:** the gate is **unlimited AI** — unlimited Gmail/PDF imports (free allowance: 10–15 per trip), unlimited transit-direction generations, unlimited Discover, and the planned AI Travel Agent. Rationale: at a near-impulse launch price the paid tier should track real per-use cost (the Claude API calls), not gate a single feature. Everything safety-critical, offline, or zero-marginal-cost (including trip count) stays free.

**Pro pricing:** $1.99/mo · $9.99/yr, 14-day trial, no promo — deliberately far below the $39.99–$59.99/yr category to prioritize growth; a one-step increase (~$19.99/yr) follows once there's conversion data, new subscribers only. Every pre-cutoff account is grandfathered free forever, unconditionally.

---

### Grandfathering: Founding Cohort Gets Pro Forever
**Decision:** All users who sign up during the free v1.0 launch window (before v1.1 Pro activation) will be permanently grandfathered into Pro — never charged, access never expires.
**Rejected:** Retroactively applying the paywall to existing free users when v1.1 launches.
**Why:**
- Grandfathering is a loyalty signal. Early adopters take a risk on an unproven product. Rewarding them with lifetime Pro access is appropriate and creates strong word-of-mouth.
- Psychological contract: users who signed up when it was free shouldn't be surprised by a paywall on their next trip. Grandfathering honors the implicit promise of the free launch.
- Implementation is architecturally free — the `beta_access` table already supports `expires_at = null` (never expires). One SQL migration at v1.1 launch grandfathers the full founding cohort with no app update required.

**Scaling architecture:**
```sql
-- Run at v1.1 Pro launch date
INSERT INTO public.beta_access (user_id, granted_by, expires_at, notes)
SELECT id, 'auto:founder-cohort', null, 'Grandfathered — signed up during free launch month'
FROM auth.users
WHERE created_at < '2026-08-01T00:00:00Z'  -- update to actual Pro launch date
ON CONFLICT (user_id) DO NOTHING;
```

After this runs: set `ALL_FREE = false`, configure RevenueCat offerings, submit v1.1. New signups see the paywall. Founding cohort's `isPro` check hits the Supabase `beta_access` table first and returns true — they never see RevenueCat.

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

### Group Sharing Ships as a Two-Person Model, Not the Original Bigger Vision
**Decision:** The shipped sharing feature is deliberately narrow — invite one travel partner, per-item visibility (visible to both, or private to whoever added it), enforced at the database level. That's it.
**Rejected:** The originally-scoped "Group / Couple Mode" — color-coded per-traveler itinerary views, separate trip-structure modes for traveling together vs. apart, per-person passport/insurance/emergency profiles, in-app trip chat, and a pre-trip checklist with task assignment.
**Why:** The bigger vision was still sitting in the product's own feature list as if it were built, discovered during an app-store-readiness review. Rather than either quietly ship the mismatch or scramble to build the full vision under review pressure, the call was to ship what actually solves the core need (two people seeing the same itinerary, with a privacy escape hatch) and be explicit that the rest is a real future direction, not a broken promise. A narrower, correctly-described feature beats a broader, inaccurately-described one.

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

## Cost & Infrastructure Decisions

### Real-Time Flight Status/Gate Alerts: Paused, Cheaper Alternative Shipped Instead
**Decision:** Instead of building real-time flight status/gate alerts as requested — which would require upgrading to a paid flight-data API tier ($49.99/mo) plus new push-notification infrastructure — flight duration/layover display now reads the duration airlines already print as plain text in confirmation emails, falling back to a computed estimate only when nothing is stated.
**Rejected:** Building the full real-time-alerts feature now, absorbing the new recurring cost and infrastructure build immediately.
**Why:** The AI email-parsing pipeline already extracts confirmation numbers and flight numbers from the same emails — it just wasn't reading the duration/layover text that was already sitting there. That gets most of the practical value (knowing how long your flight or layover will be) at effectively zero incremental cost. The paid upgrade stays on the roadmap, tied to concrete future triggers (revenue threshold, support tickets about unverified future flights, approaching the free tier's request cap) rather than being silently dropped. When a feature request implies a recurring cost, the first question is what data already gets most of the value for free — not whether to pay for it.

### In-App Trip Chat: Parked Indefinitely
**Decision:** Trip chat stays on the roadmap but implementation is deliberately not decided — the real open question (an AI assistant vs. peer-to-peer messaging between travelers) needs more user feedback before it can be scoped responsibly.
**Rejected:** Building either version now to make forward progress, or defaulting to whichever is technically simpler.
**Why:** The two versions solve genuinely different problems and would be built completely differently. Guessing now risks building the wrong one and having to redo it once real feedback arrives. This was previously miscategorized as blocked by the same open question as phrase translation (see UX Decisions) — it isn't, and decoupling them let translation ship on its own timeline.

### Offline Map Tiles: Descoped
**Decision:** True offline map tiles (downloading map imagery for full offline use) are out of scope for the map view feature.
**Rejected:** Building offline tile support as part of the initial map view.
**Why:** Judged not valuable enough to customers relative to its cost and complexity — and building it only partially would sit in real tension with the app's core "everything works offline" positioning. Better to not promise it than to promise it and deliver something half-working.

---

## Persona Decisions

### Hiking / Hut-to-Hut Travel Added as a Target Persona
**Decision:** Multi-day trail and hut-to-hut travel is now an explicit persona the product designs for — Discover's AI suggestion engine now surfaces trail systems and hut-to-hut routes as legitimate suggestions, not just restaurants and city sights, when relevant to a traveler's interests.
**Why:** This style of travel has grown significantly and wasn't represented anywhere in how the product evaluates what to suggest to travelers. A product built for "self-planned, multi-country travel" was implicitly assuming a city-to-city itinerary shape. Naming the persona explicitly means every future Discover/itinerary feature gets evaluated against it too, not just this one prompt change.

---

## Process Decisions

### Demo Panels at the End of Every Phase
**Decision:** Every phase ends with a structured review by six disciplines before it closes.
**Why:** A solo PM-led build has no built-in check on blind spots. No one is reviewing the architecture for scalability risks, no one is catching UX trust issues, no one is doing QA-level edge case analysis. The demo panel creates cross-functional accountability. It also creates a record — the DEVLOG documents not just what was built but what everyone thought about it. That's the difference between shipping fast and shipping blind.

### Never Close a Phase Without a DEVLOG Entry
**Decision:** No phase is marked done without a written record of decisions, problems solved, and panel feedback.
**Why:** The code shows what was built. The DEVLOG shows how decisions were made. For a solo project, this is the equivalent of a product council decision memo — it forces reflection and creates an artifact that explains the reasoning behind every significant choice.

### Ambiguous Features Require Sign-Off Before Build Starts
**Decision:** For features with a genuinely ambiguous interaction design or technical approach (not every feature — most are unambiguous enough to just build), an architecture reviewer and a product-requirements reviewer must both sign off on the written spec before a single line of code is written.
**Why:** Applied for the first time to PDF/photo itinerary import, a feature with real open questions (how photo capture should flow, how compression should work, what disclosure a user needs before a document leaves their device). Both reviewers found real, fixable problems on the first pass — a disclosure screen that would show the wrong copy depending on which import path a user took, a client/server timeout mismatch that would cause spurious failures on real usage, and an incorrect assumption that some new work could reuse code that didn't actually exist yet. All three were cheap to fix on paper, before any code existed to rewrite. The alternative — building first and discovering these during QA or in production — is strictly more expensive the later it's caught.

### High-Stakes Reviews Verify Against the Live System, Not Just the Diff
**Decision:** For anything touching production data access (permissions, auth, cross-account data flows), a review isn't complete from reading code alone — it has to include a real check against the live system's actual behavior.
**Why:** A security-focused review of a database-permission change read the code, read the migration's own comment claiming a particular pattern was "safe," and then verified that claim directly against the live database instead of trusting it — and found the claim was wrong. The change had silently broken every account's data sync in production, masked only because the app happens to fall back to cached data on any failed request. A code-only review would very plausibly have signed off on the confident, wrong comment. The fix and the lesson both matter: the fix was fast once found, but it was only found because "verify the claim, don't just read it" was the standard for this class of change.

### Fix Every Finding a Review Surfaces, Not Just the Severe Ones
**Decision:** When a structured review (the demo panel, a Build Gate pass) returns findings, the default is to fix all of them in the same pass — including the ones tagged low-severity — rather than triaging down to "blockers only."
**Why:** Several of the lower-severity findings in one review pass (a stale code comment describing behavior that had actually changed, an inconsistent button-label capitalization, a missing loading indicator) were cheap enough that deferring them would have cost more in tracking overhead than just fixing them on the spot. Reserving "skip it" for genuinely large, separately-scoped work (a large file needing a dedicated refactor, a data-model change worth its own session) keeps the backlog meaningful instead of becoming a dumping ground for anything not on fire.

### Manual Testing Is a Sign-Off, Not a Defect Hunt
**Decision:** Every manual testing document is now written to state up front, explicitly, everything already confirmed by automated tests and live-verified runs — so a human pass only ever covers what genuinely requires one (real cross-account behavior, a visual confidence check, final submission steps), never re-litigating what's already proven.
**Rejected:** Scattered, feature-by-feature manual test documents that don't distinguish "verified elsewhere" from "needs your eyes," implicitly asking the tester to re-check everything.
**Why:** A founder's manual testing time is the scarcest resource in a one-person build process. Spending it re-confirming things automation already caught is a worse use of that time than spending it on the small number of things that structurally require a human — and it trains the habit of treating every test pass as a hunt for anything wrong, which is exhausting and doesn't scale. Making the automated coverage explicit and visible turns manual testing into a fast confidence check instead of an open-ended search.

### Collect Less Instead of Disclosing More
**Decision:** When a privacy review finds data the policy doesn't mention, the first choice is to stop collecting or keeping it, not to add a disclosure. Applied in October 2026: analytics IP addresses are now discarded, passport nationality was removed from analytics events, sign-in security logs are deleted after 30 days, and leftover placeholder records from account deletions were removed.
**Rejected:** Keeping the data and adding a sentence to the privacy policy for each item.
**Why:** Every disclosed item is a promise to maintain, a question a reviewer will ask, and data that can leak. None of these items was being used for a decision. The one real cost was losing country-level breakdowns in analytics, which nobody was using.

### A Test Isn't Done Until Breaking the Code Fails It
**Decision:** For any new test that guards a privacy or security behavior, the reviewer deliberately breaks the code it protects (removes the check, widens the permission, logs the forbidden value) and confirms the test goes red.
**Why:** A review found that removing the very line that stops email attachments from being downloaded left all 1,100+ tests green. The test checked a helper function on its own, not the behavior users depend on. Mutation checks found several more tests like it that passed for the wrong reasons.
