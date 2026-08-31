# Roam Wyld — Success Metrics & PostHog Instrumentation Plan

Canonical mapping of README success metrics to PostHog event names, fire triggers, and required properties. Originally written pre-launch as the Phase 5 instrumentation plan — the app has since shipped and actual event names have diverged from this plan in places as features were built. See **Implementation Status** below for what's confirmed live vs. still planned-not-built, verified against `src/lib/analytics.ts` as of 2026-08-02. Where they conflict, the code is the source of truth, not this document.

---

## Implementation Status (verified 2026-08-02)

| Metric | Status | Note |
|---|---|---|
| 1. Trip Completion Rate | ⚠️ Partial | `trip_created` and `trip_setup_completed` are real; `traveler_profile_saved` was never built |
| 2. Booking Import Success Rate | ✅ Live | `import_started/parsed/completed/failed` all real — `method` now includes `'photo'` (PDF/photo import shipped 2026-08-02), not just `'email'\|'pdf'` as originally planned |
| 3. Validation Pass Rate | ❌ Not built | No `booking_validation_*` events exist |
| 4. Daily Active Use During Trips | ⚠️ Different shape | No `app_opened`/`trip_viewed` — `session_started` and `today_screen_trip_state` exist instead, narrower than planned |
| 5. Transit Direction Usage | ⚠️ Different shape | Real event is `transit_directions_viewed` (+ `cache_served`), not the planned `directions_requested/viewed/regenerated` trio |
| 6. Free-Time Suggestion Tap Rate | ❌ Not built | No `suggestions_shown/tapped/saved` events — Discover and the trip wishlist feature both shipped without dedicated analytics |
| 7. Return Trip Creation Rate | ✅ Live | `trip_created` carries `trip_number` as planned |
| 8. Group Invite Rate | ⚠️ Different shape | Real event is `travel_partner_added`, no `group_mode_enabled` |
| 9. Alert Engagement Rate | ❌ Not built | Real-time flight alerts feature itself is paused (see ROADMAP.md) — no alert events exist |
| 10. Offline Reliability | ⚠️ Narrower | `cache_served` exists but only for transit directions, not the general `offline_feature_*` trio across all offline features as planned |
| 11. Crash-Free Session Rate | ✅ Live | `session_started` real; crash-free rate itself tracked via Sentry, not PostHog, as originally planned |
| 12. Trip Share Usage | ❌ Not built | No `trip_shared`/`trip_share_accepted` events |

**Real events that exist but aren't represented in this plan at all:** the Phase 4 companion features (currency converter, language phrases, emergency numbers, insurance, affiliate links) each have their own dedicated events not mapped to any of the 12 metrics above — see `src/lib/analytics.ts` directly for the current full list rather than relying on this document for those.

---

## Event Naming Convention

- **Format:** `snake_case`, verb + noun
- **Required properties on every event:** `trip_id` (where applicable), `platform: 'ios'`, `app_version`
- **Never include PII:** no emails, passport numbers, confirmation codes, or full names in event properties
- **Super properties (set once on login):** `user_id`, `platform`, `app_version`

---

## Activation Metrics

### 1. Trip Completion Rate
**Definition:** % of users who complete full trip setup (bookings added + traveler profile filled in)

| Step | Event Name | Trigger | Key Properties |
|------|-----------|---------|---------------|
| Trip created | `trip_created` | `createTrip()` succeeds | `destinationCount`, `durationDays`, `trip_number` (actual params — no `import_method` on this event) |
| First booking added | `booking_added` | `createBooking()` succeeds | `type`, `source: 'manual'\|'gmail_import'\|'pdf_import'\|'photo_import'` |
| Traveler profile saved | `traveler_profile_saved` | Profile save succeeds | ❌ Not built — no event exists for this step |
| Trip setup completed | `trip_setup_completed` | User has ≥1 booking + profile filled | `trip_id`, `booking_count`, `import_methods_used: string[]`, `destination_count` |

**Funnel:** `trip_created` → `booking_added` → `trip_setup_completed`

---

### 2. Booking Import Success Rate
**Definition:** % of email/PDF/photo imports that parse all bookings without manual correction

| Event | Trigger | Key Properties |
|-------|---------|---------------|
| `import_started` | User taps Gmail/PDF/photo import | `method: 'email'\|'pdf'\|'photo'` |
| `import_parsed` | Parser returns results | `method`, `bookings_found: number`, `parse_errors: number` |
| `import_completed` | User confirms import | `method`, `bookings_imported: number`, `bookings_deselected: number` |
| `import_failed` | Parser throws / returns 0 results | `method`, `error_type: string` |

**Metric query:** `import_completed` events where `parse_errors = 0` / total `import_started`

---

### 3. Validation Pass Rate
**Definition:** % of manually entered bookings that pass validation on first attempt

| Event | Trigger | Key Properties |
|-------|---------|---------------|
| `booking_validation_attempted` | Save tapped on booking form | `booking_type`, `is_manual_entry: bool` |
| `booking_validation_passed` | Validation succeeds | `booking_type`, `attempt_number: 1\|2\|3+` |
| `booking_validation_failed` | Validation rejects | `booking_type`, `failure_reason: 'invalid_confirmation'\|'hotel_not_found'\|'format_error'` |

---

## Engagement Metrics

### 4. Daily Active Use During Trips
**Definition:** % of active trips with at least one app open per travel day

| Event | Trigger | Key Properties |
|-------|---------|---------------|
| `app_opened` | App foregrounds | `has_active_trip: bool`, `days_until_trip_start: number`, `days_into_trip: number` |
| `trip_viewed` | User opens a specific trip | `trip_id`, `trip_status: 'upcoming'\|'active'\|'past'` |

**Metric query:** For trips where today is between `start_date` and `end_date`, count distinct travel days with at least one `app_opened` event where `has_active_trip = true`

---

### 5. Transit Direction Usage
**Definition:** % of travel days where step-by-step directions are accessed

| Event | Trigger | Key Properties |
|-------|---------|---------------|
| `directions_requested` | "Get Directions" button tapped | `trip_id`, `is_cache_hit: bool`, `segment_count: number` |
| `directions_viewed` | Directions modal opens | `trip_id`, `is_offline: bool` |
| `directions_regenerated` | Regenerate tapped | `trip_id`, `reason: 'manual'` |

---

### 6. Free-Time Suggestion Tap Rate
**Definition:** % of free-time blocks where a suggestion is tapped or saved

| Event | Trigger | Key Properties |
|-------|---------|---------------|
| `suggestions_shown` | Suggestions section renders | `trip_id`, `suggestion_count: number`, `is_cached: bool` |
| `suggestion_tapped` | User taps a suggestion card | `trip_id`, `suggestion_type: 'restaurant'\|'activity'`, `is_offline: bool` |
| `suggestion_saved` | User saves/bookmarks a suggestion | `trip_id`, `suggestion_type` |

---

## Retention Metrics

### 7. Return Trip Creation Rate
**Definition:** % of users who create a second trip within 6 months of first trip end date

| Event | Trigger | Key Properties |
|-------|---------|---------------|
| `trip_created` | (same as activation) | `trip_number: number` (1st, 2nd, 3rd trip) |

**Metric query:** PostHog retention analysis — users who fired `trip_created` twice, with ≤180 days between events

---

### 8. Group Invite Rate
**Definition:** % of trips that add a second traveler

| Event | Trigger | Key Properties |
|-------|---------|---------------|
| `traveler_added` | Second traveler saved to trip | `trip_id`, `traveler_count: 2\|3\|4+` |
| `group_mode_enabled` | Group/couple mode turned on | `trip_id` |

---

### 9. Alert Engagement Rate
**Definition:** % of alerts not dismissed immediately (proxy for perceived value)

| Event | Trigger | Key Properties |
|-------|---------|---------------|
| `alert_delivered` | Push notification sent | `trip_id`, `alert_category: 'flight'\|'hotel'\|'activity'\|'border'`, `hours_before: number` |
| `alert_opened` | User taps notification | `trip_id`, `alert_category`, `time_to_open_seconds: number` |
| `alert_dismissed` | Notification dismissed without opening | `trip_id`, `alert_category` |

**Metric query:** `alert_opened` / (`alert_opened` + `alert_dismissed`) per `alert_category`

---

## Quality Metrics

### 10. Offline Reliability
**Definition:** % of offline feature requests that succeed without error

| Event | Trigger | Key Properties |
|-------|---------|---------------|
| `offline_feature_requested` | Any feature accessed while `is_offline = true` | `feature: 'trip'\|'directions'\|'entry_requirements'\|'emergency'\|'currency'\|'language'`, `trip_id` |
| `offline_feature_succeeded` | Feature loads from cache successfully | `feature`, `trip_id`, `cache_age_hours: number` |
| `offline_feature_failed` | Cache miss or error while offline | `feature`, `trip_id`, `error_type: string` |

**Metric query:** `offline_feature_succeeded` / `offline_feature_requested` by `feature`

---

### 11. Crash-Free Session Rate
**Tracked via Sentry** — not PostHog. Target: ≥ 99.5%

PostHog complement: track session starts for denominator
| Event | Trigger | Key Properties |
|-------|---------|---------------|
| `session_started` | App cold launch or foreground after 30min | `is_offline: bool`, `has_active_trip: bool` |

---

## Growth Metrics

### 12. Trip Share Usage
**Definition:** % of trips where the share/invite feature is used at least once

| Event | Trigger | Key Properties |
|-------|---------|---------------|
| `trip_shared` | Share sheet opened for a trip | `trip_id`, `share_method: 'link'\|'invite'\|'screenshot'` |
| `trip_share_accepted` | Recipient opens shared trip link | `trip_id`, `is_new_user: bool` |

---

### 13. Timeline Structure Metrics
**Added 2026-08-25** (data-analyst-caught gap during a feature review — instrumented the same session, not deferred). **Definition:** how often the itinerary timeline's structural features (Anytime grouping, layover detection, different-city notes) actually show up, to validate whether they're worth their engineering investment and whether a future auto-suggestion engine would be solving a frequent problem.

| Event | Trigger | Key Properties |
|-------|---------|---------------|
| `anytime_section_rendered` | Trip-detail view loads with ≥1 untimed booking anywhere in the trip | `tripId`, `anytimeBookingCount: number` |
| `layover_divider_rendered` | Layover pairing produces ≥1 pair that renders | `tripId`, `pairingTier: 'connection_group_id'\|'heuristic'`, `minutesBetween: number` |
| `different_city_note_shown` | A different-city transition fires ≥1 time in the trip | `tripId`, `noteCount: number` |

**Caveat, not a data artifact:** `different_city_note_shown`'s rate is not reliable ground truth for "how often trips are actually multi-city" — the underlying destination match is a known-imprecise fuzzy text match, flagged during the same review. This metric measures the note's fire-rate given that imprecision, not real multi-city frequency, until the matching itself is tightened.

**How to apply:** `anytime_section_rendered`'s count is a direct go/no-go input for the "suggest a time" feature (built 2026-08-31, kept behind an off flag). That feature stays dark until the instrumentation can actually answer whether long unscheduled gaps are common enough to be worth a dedicated interaction — the current event lacks a clean baseline (it only fires when the count is already non-zero, and it counts booking types the feature doesn't address). Next step: a trip-load event that fires regardless of count.

---

## Implementation Notes

### PostHog Setup (React Native)

```ts
import PostHog from 'posthog-react-native'

export const posthog = new PostHog(process.env.EXPO_PUBLIC_POSTHOG_KEY!, {
  host: 'https://app.posthog.com',
  captureMode: 'form',
  persistence: 'file',
  disabled: false,
})

// Set super properties once on login:
posthog.identify(userId, { platform: 'ios', app_version: Constants.expoConfig?.version })

// Opt-out flow (required for privacy):
posthog.optOut()  // call from Settings > Privacy > Disable Analytics
```

### Properties Never to Include

- Email address
- Passport number
- Booking confirmation codes
- Hotel/airline names (could be used to reverse-identify trips)
- Full names (first or last)
- Device IP address

### Phase 5 Instrumentation Checklist

Before TestFlight submission, verify these events fire in a test session:
- [ ] `trip_created` fires on new trip save
- [ ] `trip_setup_completed` fires after first booking + profile
- [ ] `booking_added` fires for each booking type
- [ ] `directions_requested` fires from Get Directions button
- [ ] `offline_feature_requested` fires when device in airplane mode
- [ ] `app_opened` fires on cold launch
- [ ] `session_started` fires on cold launch
- [ ] All events visible in PostHog Live Events stream
- [ ] No PII appears in any event property

---

## PostHog Dashboards to Build (Phase 5)

| Dashboard | Key Charts |
|-----------|-----------|
| Activation | Trip completion funnel; import success rate by method |
| Engagement | DAU during trips; directions usage rate; offline reliability by feature |
| Retention | 30/60/90 day return trip creation; alert engagement by category |
| Quality | Offline success rate trend; crash-free rate (Sentry embed) |
| Growth | Trip share rate; new users from shared links |
