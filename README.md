# Compound Tally Tracker

> A WearOS app for fast, compound people-counting across demographic categories — built for small round watch screens.

[![Platform](https://img.shields.io/badge/platform-WearOS-blue)](https://wearos.google.com)
[![Language](https://img.shields.io/badge/language-Kotlin-orange)](https://kotlinlang.org)
[![UI](https://img.shields.io/badge/UI-Jetpack%20Compose%20for%20Wear-green)](https://developer.android.com/training/wearables/compose)

---

## What It Does

Compound Tally Tracker lets you quickly count people by demographic group using a swipeable, dial-style interface optimized for round WearOS watch faces. Instead of one global counter, each screen shows a **radial dial** with 4–6 labeled category slots around a central button — tap the button to increment, swipe to switch screens.

Perfect for: researchers, retail staff, event counters, field surveyors.

---

## Screens

| Screen | Category Type | Sub-categories |
|--------|--------------|----------------|
| 1 | **Male Adult** | 20s · 30s · 40s · 50s · 60s · 70s |
| 2 | **Female Adult** | 20s · 30s · 40s · 50s · 60s · 70s |
| 3 | **Young Adult** | Office Worker (M/F) · Univ. Student (M/F) |
| 4 | **Student** | High Sch. Student (M/F) · Middle Sch. Student (M/F) |

Each screen uses a **circular radial layout** — categories at N/E/S/W positions around a central tappable orb. Swiping left/right navigates between the 4 screens.

---

## UI Design

- **Round-first layout** using `ScalingLazyColumn` and `Compose for Wear OS`
- Central orb button glows blue/teal on press (haptic feedback)
- Category labels in bold cyan text, count displayed beneath each
- Avatar icon + category name shown in top avatar row for current screen context
- All counts persist in-session; long-press center to reset screen
- **Bottom FAB**: `+` to add count to last-selected category; `-` button cancels/undoes

---

## Tech Stack

- **Language**: Kotlin
- **UI**: Jetpack Compose for Wear OS
- **Navigation**: `SwipeDismissableNavHost` with 4 pager destinations
- **State**: `ViewModel` + `StateFlow` per screen
- **Persistence**: `DataStore` (counts survive app backgrounding)
- **Min SDK**: Wear OS API 30 (Android 11)
- **Target SDK**: Wear OS API 34

---

## Getting Started

```bash
git clone https://github.com/Quazmoz/CompoundTallyTracker.git
cd CompoundTallyTracker
./gradlew assembleDebug
```

Deploy to a WearOS emulator or physical device via Android Studio.

---

## License

MIT
