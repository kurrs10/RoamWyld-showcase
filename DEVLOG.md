# VOYAGR — Build Log

A running record of what was built each phase, the decisions made, problems solved, and what the cross-functional review panel said. Most recent phase first.

Built by Kirsten Evans (Product Manager) using Claude Code.

> **Note:** This is a sanitized version of the build log. Implementation-level details (file paths, code snippets) are omitted. The decisions, tradeoffs, and feedback are complete.

---

## Phase 3 — The Differentiators
**Dates:** Completed May 20, 2026 — ahead of roadmap target (June 3–16)
**Goal:** Build the features no competitor has

### What Shipped

**Offline Cache Architecture**
Full SQLite offline cache layer using expo-sqlite (WAL mode). Supabase-first reads with write-through caching on success and network-error-only fallback — auth errors are never swallowed and always propagate to force re-authentication. Cache writes on create AND edit so offline reads are immediately consistent after any mutation. Deleted server-side trips are purged from cache on next sync. Offline amber banner surfaces in the UI when serving from cache.

**Entry Requirements**
45-country static dataset covering all Schengen members, UK, Ireland, Turkey, Japan, South Korea, Thailand, Vietnam, Singapore, Indonesia, India, the Middle East, Africa, Oceania, and the Americas. Five passport nationalities (US, UK, AU, CA, EU). Collapsible UI section below the bookings timeline: nationality selector pills, per-destination visa badges (color-coded by requirement type), days permitted, per-passport checklist, and notes. Schengen counter with 80-day warning threshold.

**Transit Directions**
AI-generated step-by-step directions via Claude Haiku, proxied through a Supabase Edge Function so the API key never touches the app bundle. JWT verification before every Anthropic call. Client caches results locally for offline use. Cache invalidates automatically on any booking create, update, or delete. Directions modal shows per-segment cards (mode, route, duration, estimated cost, local tips), AI disclaimer, generation timestamp, and a Regenerate button.

**UI Refresh**
Full palette: primary teal replacing blue across all interactive elements, amber verified badges, emerald destination tags, proper contrast tiers throughout.

---

### Key Product Decisions

**Network-error-only cache fallback (not all errors)**
The offline fallback catches network failures but deliberately lets auth errors propagate. A JWT-expired user should be prompted to re-authenticate, not silently served stale data from a previous session. This is a product safety decision as much as a technical one.

**Claude API routed through backend, never the app bundle**
API keys in a mobile app bundle are extractable. All Anthropic calls proxy through a Supabase Edge Function with JWT verification. This is non-negotiable for security and was designed in before writing the first line of the feature.

**Schengen counter only fires for all-Schengen trips**
In a mixed trip (France + Japan), hotel nights can't be attributed to specific countries with confidence. Rather than show a potentially misleading day count, the counter is suppressed for mixed trips. Individual country cards still show the correct visa rules for every destination. Showing nothing is better than showing wrong.

**Group mode and customizable alerts deferred to Phase 4**
The CPO demo panel in Phase 2 recommended this: Phase 3 was already carrying offline architecture + transit directions + entry requirements — three of the four "Never Cut" features. Adding group mode would split focus and risk none of it landing well. Deferred cleanly to Phase 4.

---

### Problems Solved

| Problem | Root Cause | Resolution |
|---------|-----------|-----------|
| Database race condition on cold start | Two simultaneous startup calls could each open a database handle before the first completed | Stored the initialization promise so concurrent callers share one initialization |
| Offline edit inconsistency | Cache write was present on booking create but missing from booking update | Added cache write to the update path; both paths now consistent |
| Schengen counter wrong for same-day stays | A zero-night stay was triggering a fallback count equal to the number of destinations | Removed fallback; zero-night stays correctly return zero days |
| Directions cache not clearing after booking edits | Cache clear was only wired to the delete path | Added cache invalidation to create, update, and delete paths |
| Duplicate destinations bypassed directions guard | Guard was counting array length, not unique values | Changed to count distinct destinations; duplicates no longer inflate the count |
| AI response unparseable | Claude wraps JSON output in markdown code fences even when instructed not to | Strip fences before parsing; return a structured error on failure |

---

### Demo Panel Feedback
*Phase 3 demo held May 20, 2026. Panel: mobile engineer, engineer architect, UX researcher, UX designer, QA expert, executive product director.*

**Mobile Engineer — Verdict: Solid**
SQLite foundation is production-quality. Two issues required fixes before phase close: the database race condition on cold start and the missing cache write on booking updates. Both fixed inline. Flagged that offline detection logic is duplicated between the UI layer and service layer — acceptable for beta, consolidate in Phase 4.

**Engineer Architect — Verdict: Conditional go (with fixes applied)**
Architecture is correct. Flagged that the transit directions cache is keyed by trip ID only — a schema migration may be needed in Phase 4 if per-segment granularity is added. Also noted the booking cap in the edge function was silently dropping by insertion order rather than chronological order; fixed to sort by date before truncating.

**UX Researcher — Verdict: Three trust issues before TestFlight**
Transit directions had no AI disclaimer or generation timestamp — real travelers would assume prices and schedules are authoritative. Added disclaimer ("AI-estimated — verify before travel") and timestamp. Two issues logged for Phase 4: the amber verified badge reads as caution/warning to most users rather than confirmation; city-level destination input silently falls back to "No data" without any hint at input time that the lookup requires a country name.

**UX Designer — Verdict: Mostly solid, two contrast failures and trip name truncation must fix**
Offline banner text and directions button text both have marginal contrast at small sizes — legibility risk in direct sunlight on OLED screens. Increase to 13px. Entry requirements section title visually disappears into the timeline — needs more weight. Trip name can overflow without truncation. Collapsible sections need animated height transitions — a static chevron toggle feels broken on iOS. Passport pills horizontal scroll has no right-edge fade affordance.

**QA Expert — Verdict: Five gate test cases defined for Phase 4**
Two P1 bugs found and fixed inline (empty cache crash on first load; duplicate destination bypass). Five test cases defined as mandatory before Phase 4 closes: edit-then-offline consistency, concurrent cold-start safety, same-day Schengen calculation, post-edit directions regeneration, and city-level destination graceful failure.

**Executive Product Director — Verdict: Phase 3 is a go. Strategic risk is Phase 4 scope.**
All four Never-Cut items shipped. Phase 4 has 12 features in 7 days — that's a wishlist, not a plan. Weather and packing list cut to v1.1; they're not moats. Group mode time-boxed to 3 days maximum — if not shippable by June 20, it moves to v1.1 to protect Phase 5. Revised Phase 4 priority order: Emergency info → Currency/tipping → Language basics → Group mode (3-day timebox) → Insurance storage → eSIM (cut if needed).

---

## Phase 2 — Smart Import + Validation
**Dates:** Completed ahead of schedule
**Goal:** Make getting trip data into the app fast and trustworthy

### What Shipped

- **Database schema migration:** Added import source tracking, validation status, validation timestamps, and raw response storage to bookings; status and sync columns to trips; group trip membership table with row-level security
- **Session management:** React context providing auth session throughout the app, eliminating redundant re-authentication on every save
- **Service layer:** Thin Supabase wrappers for all data operations, structured for clean offline swap in Phase 3
- **EAS Build configuration:** iOS dev, preview, and production build profiles
- **Blocking pre-save validation:** Flights verified against AviationStack; hotels against Google Places. Save is blocked if the booking can't be verified. Button shows "Validating..." during the check.
- **Validation badges:** Verified and Unverified states on every booking card in the timeline
- **Gmail OAuth import:** Token flow → Gmail REST API → regex parsing → review modal with per-booking checkboxes → batch insert. Partial failure handled gracefully — one bad booking doesn't drop the rest.
- **Edit and delete bookings:** Long-press action sheet → pre-populated modal → re-validates on save

---

### Key Product Decisions

**Blocking validation (not background)**
Early design ran validation in the background after the booking was saved. Changed to validate-first after a core product decision: a user should not be able to save a booking that doesn't exist. The validation API layer was separated into check functions (API call only, no DB write, used pre-save) and validate functions (API + DB write, available for future re-validation). This separation allows re-validation without duplicating the API logic.

**Gmail imports skip validation by default**
Bookings from real confirmation emails are trusted — validation skipped, status set to "skipped." Running AviationStack and Google Places on every email-parsed booking would mean 20+ API calls per import session, many failing on regional carriers and boutique hotels not in the validation databases. The badge system still surfaces any future re-validation results. Validation is for manual entry where typos are the risk — not for confirmed emails.

**Outlook import deferred**
Gmail covers ~35% of email users; the VOYAGR target persona skews heavily Gmail. Outlook adds a second OAuth integration surface and targets a different (enterprise) demographic. Deferred to v1.1 — will revisit after beta feedback confirms the demand.

**PDF upload deferred**
Listed in the roadmap as a safe-to-cut item. Email import covers the high-confidence confirmation case. PDF parsing is format-dependent, has a high error rate, and the incremental coverage over email import doesn't justify the Phase 2 time. Deferred to v1.1.

**Group mode moved to Phase 4**
CPO panel recommended moving group mode out of Phase 3. It touches the entire data model and would split focus during the offline architecture sprint. Phase 3 needed full attention on the Never-Cut features (offline cache, transit, entry requirements).

---

### Problems Solved

| Problem | Root Cause | Resolution |
|---------|-----------|-----------|
| Flight validation silently failing on iOS devices | iOS App Transport Security blocks non-HTTPS; validation API uses HTTP | Added ATS exception for the validation API domain in app config |
| Gmail OAuth redirect failing on real device | Redirect URI in code didn't match the scheme defined in app config | Fixed to use correct app scheme |
| Edit flight with no flight number triggering hotel validation | Conditional logic had an else-if where two independent ifs were needed | Split into independent conditionals |
| Flight number format mismatch | Input allowed "UA 123"; API expects "UA123" | Added whitespace normalization before API call |
| Edited booking retaining old validation badge | Update function wasn't resetting validation status | Added status reset to every update payload |
| Gmail batch import losing data on partial failure | Fail-fast behavior — one bad insert dropped everything | Replaced with parallel-with-individual-errors; partial failure shows a clear message |

---

### Demo Panel Feedback
*Phase 2 demo held May 20, 2026. Full panel + QA engineer.*

Validation UI earned strong marks across the board — the distinction between Verified/Unverified badges was clear and the blocking save behavior was validated as the right UX call. QA found partial failure handling as the critical gap (fixed). UX recommended adding a "why can't I save this?" explainer when validation fails. Architect recommended pre-sort by date before any booking cap is applied. CPO recommended moving group mode and alerts to Phase 4 to protect Phase 3 focus.

---

## Phase 1 — Foundation
**Dates:** May 13–19, 2026
**Goal:** Working app shell with navigation, auth, manual trip entry, and timeline view

### What Shipped

- Expo SDK 54 project scaffold (TypeScript, iOS target)
- Supabase backend: auth, Postgres schema, row-level security policies
- User account creation and sign-in (email + password)
- Bottom tab navigation: Today, Trip, Discover, Profile
- Manual trip creation: name, multi-destination tag input, date range
- Booking entry for three types: Flights (confirmation #, flight number, airports, round-trip toggle with return date), Hotels (check-in/check-out with date constraints), Activities (date + notes)
- Day-by-day timeline view: bookings grouped by date, sorted chronologically, "Unscheduled" bucket for undated items
- Traveler profile: read mode and edit mode (name, passport nationality, insurance reference)
- App running on physical iPhone via Expo Go

---

### Key Product Decisions

**Tag-based multi-destination input**
Destinations stored as an array. The UI uses a tag input pattern: type a destination, press Enter, it becomes a removable pill chip. Simpler than a structured country picker at this stage — will revisit in Phase 3 when entry requirements need to resolve specific countries from the input.

**Inline iOS date pickers**
Calendar inline below the tapped field rather than in a modal. More native-feeling for a date-heavy booking form — an opinion informed by iOS HIG and UX panel feedback from the Phase 1 demo.

**Architecture spike before Phase 2**
Before starting Phase 2, made three foundational decisions that would have been expensive to change later:
1. **Offline storage:** expo-sqlite (built into Expo SDK 54, sufficient for all caching needs through Phase 4; WatermelonDB's setup overhead unjustifiable on a June 30 deadline)
2. **Transit directions:** Claude API, user-triggered batch for entire trip, cached to SQLite — not per-save auto-trigger (5–15× more expensive and fires before the itinerary is complete)
3. **Group mode data model:** Current single-owner trip schema requires a `trip_members` join table for group mode — added to the Phase 2 schema migration before Phase 3 group work begins

---

### Problems Solved

| Problem | Root Cause | Resolution |
|---------|-----------|-----------|
| "Permission denied" on database reads | Row-level security policies in place but table-level grants missing | Added explicit table grants to schema |
| Hotel bookings not appearing in timeline | Date field not populated for hotel booking type on save | Set date to check-in date for hotel bookings |
| Email confirmation redirecting to localhost | Supabase default redirect URL for email confirmation | Disabled email confirmation in Supabase Auth settings for development |
| Keyboard blocking form input fields | Modal lacked keyboard avoidance | Wrapped modal in keyboard-avoiding container |
| Date formatter crashing on "Unscheduled" | No guard on non-ISO strings passed to date formatter | Added early return for non-date strings |
| Header hidden behind iPhone notch | No safe area handling | Added safe area insets with appropriate padding |

---

### Demo Panel Feedback
*Phase 1 demo held May 19, 2026. Panel: mobile engineer, architect, UX researcher, UX designer, QA expert, executive product director.*

Navigation and booking entry praised as clean and intuitive. QA identified the date formatter crash as a P0 (fixed). UX researcher raised that "Unscheduled" bucket position at the bottom of the timeline is counterintuitive — users look for unscheduled items first to place them. UX designer noted the header notch collision (fixed) and recommended the tag input pattern for destinations as the right call for v1. Architect recommended the service layer separation before Phase 2 to set up clean offline swap in Phase 3.

---

## How This Was Built

Every phase starts with a scrum master check-in (current phase, days remaining, scope flags). Every build session ends with a QA review. Every phase ends with a structured demo panel review across six disciplines:

- **Mobile Engineer** — implementation quality, patterns, platform compliance
- **Engineer Architect** — system design, data model, scalability considerations
- **UX Researcher** — user mental models, trust, behavioral accuracy
- **UX Designer** — visual design, accessibility, motion, iOS HIG compliance
- **QA Expert** — test coverage, edge cases, gate criteria for the next phase
- **Executive Product Director** — strategic priorities, scope risk, business decisions

Feedback is logged here and incorporated before the phase closes. No phase is marked done without a completed panel and DEVLOG entry.

This structure exists because a solo PM-led build has no built-in check on blind spots. The panel creates the cross-functional accountability that would normally come from a team.
