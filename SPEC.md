# Compound Tally Tracker — Technical Specification

**Version**: 0.1.0  
**Platform**: WearOS (Wear OS 3+, round display primary target)  
**Last Updated**: 2026-04-13

---

## 1. App Overview

Compound Tally Tracker is a single-session demographic counting tool for WearOS. The user swipes through 4 screens, each representing a top-level demographic group. Each screen shows a radial dial with 4–6 sub-category counters around a central button.

---

## 2. Screen Definitions

### Screen 1 — Male Adult
- **Avatar**: Male Adult icon (brown hair, mustache)
- **Dial Categories** (6 positions — clock layout):
  - Top: `20s`
  - Right: `30s`
  - Bottom-right: `40s`
  - Bottom: `50s`
  - Bottom-left: `60s`
  - Left: `70s`

### Screen 2 — Female Adult
- **Avatar**: Female Adult icon (red/auburn hair)
- **Dial Categories** (6 positions):
  - Same age-decade layout as Screen 1: `20s` · `30s` · `40s` · `50s` · `60s` · `70s`

### Screen 3 — Young Adult
- **Avatar**: Young Adult icon (blonde/light hair)
- **Dial Categories** (4 positions):
  - Top: `Office Worker (M)`
  - Right: `Office Worker (F)`
  - Bottom: `Univ. Student (M)`
  - Left: `Univ. Student (F)`

### Screen 4 — Student
- **Avatar**: Student icon (dark bowl cut, school-age)
- **Dial Categories** (4 positions):
  - Top: `High Sch. Student (M)`
  - Right: `High Sch. Student (F)`
  - Bottom: `Middle Sch. Student (M)`
  - Left: `Middle Sch. Student (F)`

---

## 3. Navigation Model

- **Primary navigation**: Horizontal swipe (left/right) between the 4 screens using `HorizontalPager` or `SwipeDismissableNavHost`
- **Screen indicator**: 4-dot page indicator at bottom of each screen
- **No deep navigation** — all screens are top-level siblings
- Back swipe from Screen 1 → dismiss app (WearOS standard back gesture)

---

## 4. Interaction Model

### 4a. Category Selection
- The radial dial always has one **active category** (highlighted in brighter cyan or with a ring indicator)
- User taps a category label or swipes directionally to select it
- The central orb button **increments the currently selected category** on tap

### 4b. Central Orb Button
- Renders as a gradient circle (light blue center → teal/blue outer ring)
- On tap: increment active category count by 1 + short haptic pulse
- On long-press: show confirmation dialog to reset all counts on current screen

### 4c. FAB Buttons (bottom corners)
- **Bottom-right blue `+`**: Increment last-touched category
- **Bottom-right red `-`**: Decrement last-touched category (undo last tap), minimum 0

### 4d. Avatar Row (top)
- Shows all 4 avatars in a horizontal scrollable row
- Active screen's avatar is full color with white background circle; others are grayscale
- Tapping an avatar navigates directly to that screen

---

## 5. Data Model

```kotlin
data class CategoryCount(
    val id: String,           // e.g. "male_adult_20s"
    val label: String,        // e.g. "20s"
    val count: Int = 0
)

data class ScreenState(
    val screenId: String,     // e.g. "male_adult"
    val title: String,        // e.g. "Male Adult"
    val categories: List<CategoryCount>,
    val activeCategory: String
)
```

---

## 6. State Management

- `TallyViewModel`: single shared ViewModel scoped to the activity
- Each screen reads its own `ScreenState` from a `StateFlow<Map<String, ScreenState>>`
- Counts persisted via `DataStore<Preferences>` keyed by `category.id`
- Session reset clears DataStore for that screen's keys

---

## 7. Visual Design Tokens

| Token | Value |
|-------|-------|
| Background | `#000000` (AMOLED black) |
| Card / Dial background | `#FFFFFF` (white circle overlay) |
| Primary label color | `#00C8FF` (cyan blue) |
| Active orb gradient | `#00B4FF` → `#00FFC8` (blue → teal) |
| Inactive avatar tint | Grayscale 40% |
| Active avatar bg | `#FFFFFF` circle |
| Font | `Roboto` / system default |
| Label size | `12sp` (watch-safe) |
| Count size | `14sp bold` |

---

## 8. Dial Layout (Circular Radial)

For a 4-category screen, positions are at 0°, 90°, 180°, 270° (top/right/bottom/left).  
For a 6-category screen, positions are at 0°, 60°, 120°, 180°, 240°, 300°.

All labels must be within the dial circle boundary and use `Box` with `Alignment` offsets calculated from the watch face radius (typically 110–120dp for 454px displays).

---

## 9. WearOS-Specific Constraints

- **No scrolling lists** on main dial screens — all content fits within the round viewport
- **Ambient mode**: freeze counts display, dim to grayscale, disable interactions
- **Always-on display**: show minimal count summary (category name + count only)
- **Accessibility**: minimum touch target 48dp per Wear OS HIG
- **Battery**: no background work — DataStore writes are debounced 500ms
- **Screen shape**: build with `LocalConfiguration.current.isScreenRound` check; support square as fallback

---

## 10. Permissions

| Permission | Reason |
|------------|--------|
| `VIBRATE` | Haptic feedback on tap |
| None others | No network, no sensors |

---

## 11. Build Configuration

```gradle
minSdk = 30          // Wear OS 3
targetSdk = 34
compileSdk = 34
versionCode = 1
versionName = "0.1.0"

dependencies {
    implementation "androidx.wear.compose:compose-material:1.3.x"
    implementation "androidx.wear.compose:compose-navigation:1.3.x"
    implementation "androidx.datastore:datastore-preferences:1.1.x"
    implementation "androidx.lifecycle:lifecycle-viewmodel-compose:2.8.x"
}
```

---

## 12. Future Enhancements (v0.2+)

- Export session counts via Wear Data Layer to paired phone
- Configurable category labels (custom category names)
- Session history with timestamps
- Tile widget showing current session totals
- Complication showing live total count
