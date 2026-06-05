# Roam Wyld — Success Metrics & PostHog Instrumentation Plan

Canonical mapping of README success metrics to PostHog event names, fire triggers, and required properties. Every event listed here must be instrumented before Phase 5 ships.

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
| Trip created | `trip_created` | `createTrip()` succeeds | `import_method: 'manual'\|'email'\|'pdf'`, `destination_count` |
| First booking added | `booking_added` | `createBooking()` succeeds | `trip_id`, `booking_type: 'flight'\|'hotel'\|'activity'`, `import_method` |
| Traveler profile saved | `traveler_profile_saved` | Profile save succeeds | `has_passport_nationality: bool`, `has_insurance: bool` |
| Trip setup completed | `trip_setup_completed` | User has ≥1 booking + profile filled | `trip_id`, `booking_count`, `import_methods_used: string[]`, `destination_count` |

**Funnel:** `trip_created` → `booking_added` → `trip_setup_completed`

---

### 2. Booking Import Success Rate
**Definition:** % of email/PDF imports that parse all bookings without manual correction

| Event | Trigger | Key Properties |
|-------|---------|---------------|
| `import_started` | User taps email/PDF import | `method: 'email'\|'pdf'` |
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
