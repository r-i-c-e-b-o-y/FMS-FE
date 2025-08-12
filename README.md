# Feed My Sheep – Prototype App

## Overview

**Feed My Sheep**
Deliver a front-end prototype for coordinating service missionaries using Kotlin Multiplatform (KMP) so you can focus on Android now and add iOS later without rewriting logic. Prototype uses dummy data, local persistence, client-side sync/change-log, and WebSocket readiness so a Church backend can be plugged in later.

Scope:

Front-end only (shared :shared KMP module + :androidApp Android app)

Offline-first local DB (SQLDelight)

Change log + SyncManager (client-side) with WorkManager-driven periodic/background sync

Mock server in /tools to emulate REST & WebSocket pushes

Map integration using MapLibre (OSM) by default; Google Maps optional

Documentation for handoff: data dictionary, socket contract, sync protocol, integration checklist

---

## Core Features

* **Offline-first support** with automatic background sync when connected.
* **Map integration** (OpenStreetMap for prototype; can swap to LDS or other APIs).
* **Pin markers** for assignments, locations, and service opportunities.
* **Role-based accounts** (service missionaries, district leaders, mission service advisors, etc.).
* **AI integration potential** (e.g., automated assignment recommendations, natural language reporting).

---

## Tech Stack

# Feed My Sheep — Recommended KMP tech stack (concise, practical)

Below is the **focused tech stack** for a Kotlin Multiplatform (KMP) implementation that lets you **develop Android now** and add iOS later while sharing business logic, networking, sync, and models.

---

## Core languages & tools

* **Kotlin Multiplatform (KMP)** — shared `:shared` module for models, sync, repos, utils.
* **Kotlin (JVM)** — Android app UI and platform glue.
* **Swift/SwiftUI** — *future* iOS UI (will consume `:shared`).
* **Gradle (Kotlin DSL)** — build system (`build.gradle.kts`, `settings.gradle.kts`).
* **Android Studio** — primary IDE for development and debugging.
* **Xcode** — required later to build/test iOS.

---

## Shared module (`:shared`) — libraries & responsibilities

* **`kotlinx.coroutines`** — concurrency, Flow/StateFlow for reactive streams.
* **`kotlinx.serialization`** — JSON (and other) serialization for network payloads.
* **Ktor Client** — multiplatform HTTP client and WebSocket support (client modules for Android/iOS).
* **SQLDelight** — multiplatform typed SQL + local DB across Android/iOS (preferred over Room for KMP).
* **Kotlinx.datetime** — consistent datetime handling across platforms.
* **Expect/actual** or small adapter interfaces for platform specifics (file I/O, secure storage hooks).
* **Testing:** Kotlin test frameworks (Kotlin Test / Kotest) for unit tests in `commonTest`.

**Responsibilities in `:shared`:** data models, domain logic, sync core + change-log structures, repository interfaces, serialization rules, and unit tests.

---

## Android module (`:androidApp`) — libraries & responsibilities

* **Jetpack Compose** (recommended) or classic Views — UI.
* **Hilt** or **Koin (KMP compatible)** — dependency injection.
* **WorkManager** — background sync tasks / periodic sync.
* **MapLibre (Android SDK)** or **Google Maps** — map rendering & markers (MapLibre preferred for OSM/offline).
* **OkHttp & Ktor bridging** — Android transport if needed for WebSocket resiliency.
* **AndroidX libraries** — Navigation, Lifecycle, ViewModel (use Kotlin Flow + StateFlow pattern).
* **Logging/Crash:** Timber, Firebase Crashlytics (optional).
* **Local DB adapter:** SQLDelight Android driver (or Room adapter if you choose Android-only DB — not recommended for KMP).

**Responsibilities in `:androidApp`:** UI implementation, platform adapters (SQLDelight driver config, map vendor init, permissions), WorkManager wiring, debug/demo UIs, assets.

---

## iOS integration (future)

* Use **KMP-generated framework** from `:shared` to call shared logic from Swift/SwiftUI.
* Map: **MapLibre iOS** or Apple MapKit (MapManager wrapper required).
* Local DB: use SQLDelight iOS driver.
* Networking: use Ktor client configured for iOS target.

---

## Networking & real-time

* **Ktor Client** for REST + WebSocket in `:shared`.
* Message contract: JSON via `kotlinx.serialization`. Use `opId`, `ts`, `entity`, `action`, `payload` envelope.
* **Idempotency**: use `opId` (UUID) for de-duplication.
* Sync flow implemented in `:shared` (SyncCoordinator) with platform workers (WorkManager on Android; background task on iOS later).

---

## Persistence & offline

* **SQLDelight** for shared schema + typed queries; generates Kotlin interfaces used by shared code.
* **Change log table** in SQLDelight: `opId`, `entity`, `action`, `payloadJson`, `ts`, `status`.
* Local cache + repository merging logic in `:shared`.

---

## Maps & media

* **MapLibre** (OSM) — open-source, offline tile hosting, vendor-agnostic.
* Option: **Google Maps** for quicker integration (Android only) if you accept vendor lock-in.
* Encapsulate map logic in `MapManager` (androidMain), so switching vendors is localized.

---

## Serialization & data formats

* **JSON** via `kotlinx.serialization` for all client messages/REST.
* Timestamps: ISO-8601 using `kotlinx.datetime`.

---

## Dependency injection & architecture

* **Repository pattern** + **Single source of truth**: ViewModel → Repository → Shared sources (local + remote).
* Use **StateFlow** for UI state.
* DI: **Koin** has KMP support; Hilt is Android-only (use Hilt in androidMain only or Koin for consistency).

---

## Testing

* **Unit tests** in `commonTest` for shared logic (Kotlin/JVM).
* **Android instrumented tests**: JUnit + Espresso or Compose testing.
* **E2E / UI tests**: Robolectric/Detox/Appium as needed.

---

## CI/CD

* **GitHub Actions** (recommended) with:

  * Gradle build for `:shared` + `:androidApp` (unit tests, assemble debug).
  * Android lint & detekt static analysis.
  * Artifact upload for distribution (Firebase App Distribution / Play Console).
* **Fastlane** for iOS release later (TestFlight), and Play Store uploads.

---

## Docs & handoff

* **Dokka** — generate API docs from KMP shared code (for developers).
* **Sphinx + MyST or Markdown** — handoff docs (`/docs`): data dictionary, socket contract, sync-protocol, integration-checklist (human readable).

---

## Quick rationale + trade-offs

* **Why KMP?** Share sync logic, validation, and data models so iOS later only needs UI code. Saves rewrite time.
* **Why SQLDelight + Ktor?** Both are mature KMP-friendly libraries enabling truly shared DB & networking.
* **Learning curve:** moderate — KMP setup and SQLDelight require initial learning. If you need *zero* KMP overhead and only Android now, pure Kotlin (Android-only) is faster. But KMP pays off if iOS is likely.

---

## Final practical tips

1. Implement **SyncCoordinator** and models in `:shared` first — this unlocks reusability.
2. Use **SQLDelight** for local DB so you avoid reimplementing persistence for iOS later.
3. Keep UI platform-specific; keep everything else in `:shared`.
4. Encapsulate map vendor-specific logic behind `MapManager` in platform source sets.
5. Add **mock-server** tools to demo real-time sync without a backend.
---

## Folder Structure

### Android Studio

```
feed-my-sheep/
├── .gitignore
├── README.md
├── LICENSE
├── build.gradle.kts        # root Gradle (Kotlin DSL)
├── settings.gradle.kts
├── gradlew / gradlew.bat
├── /shared                 # KMP shared module (business logic + models + sync)
│   ├── build.gradle.kts
│   ├── src/commonMain/kotlin/  # shared Kotlin code (models, repos, sync core)
│   ├── src/androidMain/kotlin/ # Android-specific adapters (Room mappers, etc.)
│   └── src/iosMain/kotlin/     # placeholder for future iOS adapters
├── /androidApp             # Android app module (UI + platform glue)
│   ├── build.gradle.kts
│   └── src/main/...
├── /iosApp (optional)      # iOS app (add later when ready)
├── /tools                  # mock-server + testing scripts
│   └── mock-server/
├── /docs                   # Sphinx/markdown docs source for handoff
│   └── source/
├── /design                 # screenshots, icons, mockups
├── /samples                # JSON files used to seed DB (dummy data)
├── /ci                    # CI snippets for GH Actions / other
└── CONTRIBUTING.md
```

---

## Development Workflow

## Minimum Tooling & Prereqs
* Java 11+ (match Gradle Kotlin plugin expectations)
* Android Studio (Arctic Fox or newer) with Kotlin plugin
* Xcode (only when you add iOS later)
* Gradle and Kotlin Gradle Plugin (handled by wrappers)
* Node.js (for tools/mock-server) — optional but useful
* GitHub account & repository

## Learning Resources

**Frontend (Android Studio)**

* [Android Studio Beginner Tutorial](https://www.youtube.com/watch?v=fis26HvvDII)
* [Kotlin for Android Developers](https://developer.android.com/kotlin)


**Maps (OSM)**

* [Leaflet.js Tutorial](https://www.youtube.com/watch?v=7ZLk3TBrk9I)
* [MapLibre SDK Android](https://maplibre.org/)
