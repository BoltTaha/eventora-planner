# Eventora Planner

Eventora Planner is a Flutter HCI project focused on practical usability:
- fast event creation
- clear event visibility (all/upcoming/past/calendar)
- reminder notifications
- Google sign-in
- per-user cloud data isolation
- AI-assisted event drafting

This README explains not only **how to run** the app, but also **why** design and architecture choices were made (HCI + UX reasoning).

## 1) Project Goal

Build an event planner that is:
- easy for first-time users
- consistent in light and dark mode
- safe for multi-user usage
- robust across Android + optional iOS/Web setups
- explainable in an HCI viva/demo

## 2) What Problem It Solves

Common reminder apps fail in three ways:
- too many taps to create an event
- users lose data when switching accounts/devices
- UI becomes confusing under theme or state changes

Eventora Planner addresses these with:
- simple event forms
- AI prompt-to-form generation
- user-scoped cloud storage
- clear sign-in / sign-out flow

## 3) Core Features

- Create, edit, delete events
- List filtering: All / Upcoming / Past
- Calendar view with event markers
- Notification scheduling
- Dark mode
- Google sign-in
- Firestore-backed user + event data
- AI event draft generation (Gemini via Firebase AI Logic)

## 4) HCI / UX Principles Used

### 4.1 Visibility of System Status
- Snackbar feedback on important actions (create/update/clear/sign-in errors)
- Loading indicators for async operations (Google sign-in, AI generation)

### 4.2 User Control and Freedom
- Edit/delete controls on event cards
- Sign-out always available in Settings
- AI is assistive, not forced (manual form remains primary fallback)

### 4.3 Consistency and Standards
- Reusable card/button styles
- predictable route names and navigation
- dark/light theming through a single provider

### 4.4 Error Prevention and Recovery
- date/time validation prevents past scheduling
- async-mounted checks prevent UI crashes after await
- Firebase bootstrap fallback prevents hard crash when platform config is missing

### 4.5 Recognition Rather Than Recall
- tabbed lists + calendar markers reduce memory load
- AI converts natural language into prefilled fields to reduce typing effort

## 5) Architecture (How It Works)

The app follows a simple layered Flutter architecture:

- `screens/` UI and user interaction
- `services/` business logic and external integrations
- `providers/` app/user/theme state
- `models/` event and user entities

### 5.1 State Management
- `Provider` is used for:
  - user state
  - theme state

### 5.2 Data Flow
- UI triggers service methods
- service reads/writes local/cloud data
- provider updates state
- UI rebuilds reactively

## 6) Data Storage Design

### 6.1 Why Cloud + Local Hybrid

We use Firestore for logged-in users so data persists after cache clear/reinstall.
We keep local fallback for scenarios where Firebase is unavailable or user is local/offline.

### 6.2 Firestore Structure

- `users/{uid}`
  - `uid`, `name`, `email`, `photoUrl`, `createdAt`, `lastLoginAt`
- `users/{uid}/events/{eventId}`
  - event fields (`title`, `date`, `time`, `location`, `description`, `notificationEnabled`, ...)

### 6.3 Multi-User Isolation

Events are scoped by Firebase UID:
- account A cannot read account B events
- sign out/in switches to correct user dataset

## 7) Authentication Design

Google sign-in flow:
1. user taps "Continue with Google"
2. Firebase Auth returns user
3. user profile upserted in Firestore
4. local session flags updated
5. app navigates to home

Sign-out flow:
1. local session cleared
2. Firebase + Google sign-out called
3. user provider reset
4. auth screen reopened

## 8) AI Integration Design

AI uses Firebase AI Logic (`firebase_ai`) for structured event draft generation.

Why this approach:
- avoids deprecated direct SDK usage
- aligns with Firebase ecosystem
- easier future production hardening (App Check, Remote Config model control)

AI behavior:
- takes natural language input
- attempts strict JSON extraction
- validates date/time format
- rejects invalid or past date drafts
- falls back with user-friendly errors

## 9) Cross-Platform Compatibility Strategy

The app includes safe Firebase bootstrap logic:
- if Firebase config exists on platform => cloud features enabled
- if missing => app remains usable with local fallback

This prevents hard startup crashes on partially configured targets (iOS/Web during setup).

## 10) Security

### 10.1 Firestore Rules

Rules are user-scoped:
- only authenticated user can read/write their own profile/events

### 10.2 App Check

Recommended for production (Android: Play Integrity).
During development, warnings may appear if App Check provider is not yet installed.

## 11) Setup Guide

## 11.1 Prerequisites

- Flutter SDK installed
- Android Studio / VS Code Flutter tooling
- Firebase project configured

## 11.2 Firebase Required Setup

1. Create/select Firebase project (recommended: `eventora-planner`)
2. Register Android app:
   - package: `com.example.eventora_planner`
3. Add SHA-1 and SHA-256 fingerprints
4. Download and place:
   - `android/app/google-services.json`
5. Enable in Firebase:
   - Authentication > Google
   - Firestore Database
   - Firebase AI Logic
6. Deploy Firestore rules:

```bash
firebase use eventora-planner
firebase deploy --only firestore:rules --project eventora-planner
```

## 11.3 Run Commands

From project root:

```bash
flutter clean
flutter pub get
flutter run -d <device-id>
```

Performance test:

```bash
flutter run -d <device-id> --profile
```

## 12) Build and Distribution

### 12.1 Share with Friends (APK)

```bash
flutter build apk --release
```

Output:
- `build/app/outputs/flutter-apk/app-release.apk`

### 12.2 Play Store (AAB)

```bash
flutter build appbundle --release
```

Output:
- `build/app/outputs/bundle/release/app-release.aab`

Upload this AAB in Play Console release flow.

## 13) Demo Script (for Instructor)

1. Show login with Google
2. Create one event manually
3. Create one event with AI prompt
4. Switch to dark mode and verify readability
5. Sign out and sign in with another account
6. Show account data isolation
7. Open Firebase Console and show:
   - users collection
   - per-user events subcollection
8. Explain HCI rationale (status feedback, consistency, error prevention, reduced cognitive load)

## 14) Known Development Notes

- First debug launch on MIUI/Redmi may show frame-skip/perf logs.
- Prefer profile/release mode when evaluating perceived performance.
- Some Google Play services warnings on custom ROMs are non-blocking.

## 15) Suggested Repository Name

Recommended repo name:
- `eventora-planner`

Alternative names:
- `eventora-hci-project`
- `eventora-ai-reminder`

## 16) License

Educational use by default unless you add a formal license file.
# Eventora Planner

Eventora Planner is a Flutter-based event reminder app with:
- local + cloud-backed event management
- Google login using Firebase Authentication
- per-user event storage in Firestore
- AI-assisted event draft generation using Firebase AI Logic (Gemini)

This project was prepared as an HCI course project and focuses on practical usability.

## Key Features

- Create, edit, and delete events
- Calendar and upcoming/past event views
- Local notifications for event reminders
- Dark mode support
- Google sign-in support
- User-isolated data (account A cannot see account B events)
- AI event generation from natural language prompt

## Tech Stack

- Flutter / Dart
- Provider (state management)
- SharedPreferences (local preferences)
- Firebase Authentication (Google login)
- Cloud Firestore (user profile + user events)
- Firebase AI Logic (Gemini)
- flutter_local_notifications

## Project Identity

- App display name: `Eventora Planner`
- Android package: `com.example.eventora_planner`
- Firebase project: `eventora-planner`

## Folder Highlights

- `lib/screens/` UI screens
- `lib/services/` app services (auth, storage, notifications, AI)
- `lib/providers/` state providers
- `android/app/google-services.json` Firebase Android config
- `firestore.rules` Firestore security rules

## Setup (Android)

1. Install Flutter SDK and Android toolchain.
2. Clone project.
3. Add Firebase Android app with package:
   - `com.example.eventora_planner`
4. Download `google-services.json` and place in:
   - `android/app/google-services.json`
5. Add SHA-1/SHA-256 in Firebase (Project Settings > Your apps).
6. Enable in Firebase:
   - Authentication > Google
   - Firestore Database
   - Firebase AI Logic (Gemini)

Then run:

```bash
flutter clean
flutter pub get
flutter run -d <device-id>
```

## iOS / Web Compatibility Notes

The app now uses safe Firebase bootstrap logic:
- If Firebase is configured on a platform, cloud features are used.
- If Firebase is missing on a platform, app falls back safely instead of hard crashing.

For iOS:
- Add Firebase iOS app
- Place `GoogleService-Info.plist` in `ios/Runner`

For Web:
- Add Firebase Web app and configure FlutterFire web options

## Firestore Data Model

- `users/{uid}`
  - `uid`
  - `name`
  - `email`
  - `photoUrl`
  - `createdAt`
  - `lastLoginAt`

- `users/{uid}/events/{eventId}`
  - event fields (`title`, `date`, `time`, `location`, etc.)

## Security Rules

Current rules restrict access to owner user:

```txt
users/{userId}               -> only auth userId
users/{userId}/events/{id}   -> only auth userId
```

Deploy rules:

```bash
firebase deploy --only firestore:rules --project eventora-planner
```

## AI Event Generation

In Create Event screen:
1. Enter natural text in "Describe with AI"
2. Tap "Generate Event with Gemini"
3. Review generated fields
4. Save event

Example prompt:
- `Meeting with supervisor tomorrow at 5 PM in Lab 2`

## Multi-Account Behavior

Events are now user-scoped in Firestore and separated by UID.
- Signing in with account B does not show account A events.
- Re-login reloads that account's own events.

## Build and Share

### Debug run

```bash
flutter run -d <device-id>
```

### Profile run (better performance check)

```bash
flutter run -d <device-id> --profile
```

### Release APK (share directly with friend)

```bash
flutter build apk --release
```

Output:
- `build/app/outputs/flutter-apk/app-release.apk`

### Play Store AAB

```bash
flutter build appbundle --release
```

Output:
- `build/app/outputs/bundle/release/app-release.aab`

## Recommended Demo Flow (for Instructor)

1. Login with Google account A
2. Create event manually
3. Create event with AI prompt
4. Sign out, login with account B
5. Show account isolation (A events not visible)
6. Open Firebase Console and show:
   - users collection
   - events subcollection per user

## Known Notes

- First debug launch can show frame-skip logs on MIUI devices; profile/release is smoother.
- App Check is recommended before production release.

## License

Use for educational purposes unless you add your own license policy.
# \# 📅 Eventora Planner

# 

# A beautiful and fully offline Flutter application for managing and scheduling personal events with smart notifications. Built with Flutter and Dart, this app provides a seamless experience for keeping track of your important events without requiring any internet connection or user accounts.

# 

# \---

# 

# \## ✨ Features

# 

# \- 📝 \*\*Create Events\*\* — Add title, location, description, date and time

# \- 🔔 \*\*Smart Notifications\*\* — Get reminded before your events automatically

# \- 📅 \*\*Calendar View\*\* — See all your events in monthly or weekly calendar format

# \- ⏰ \*\*Upcoming Events\*\* — Track all, upcoming, and past events in one place

# \- ✏️ \*\*Edit and Delete\*\* — Full control over your events anytime

# \- 🌙 \*\*Dark Mode\*\* — Beautiful dark and light theme support

# \- 💾 \*\*100% Offline\*\* — All data stored locally on your device, no internet needed

# \- 🔒 \*\*Private and Secure\*\* — No accounts, no cloud, no data sharing whatsoever

# \- 📱 \*\*Clean UI\*\* — Modern Material Design 3 interface

# 

# \---

# 

# \## 📱 App Screens

# 

# | Screen | Description |

# |--------|-------------|

# | Login Screen | Enter your name to get started, no password needed |

# | Onboarding | Beautiful introduction to app features |

# | Upcoming Events | View all, upcoming, and past events in tabs |

# | Create Event | Add new events with full details and notifications |

# | Edit Event | Modify any existing event |

# | Calendar View | Monthly and weekly calendar with event markers |

# | Settings | Theme toggle, notification controls, sign out |

# 

# \---

# 

# \## 🚀 Getting Started

# 

# \### Prerequisites

# 

# \- Flutter SDK 3.7.0 or higher

# \- Dart SDK 3.7.0 or higher

# \- Android device or emulator running API 21 or higher

# \- Android Studio or VS Code with Flutter extension

# 

# \### Installation

# 

# 1\. Clone the repository

# 

# ```bash

# git clone https://github.com/Haris-Shahzad/event-reminder-app.git

# ```

# 

# 2\. Navigate to the project folder

# 

# ```bash

# cd event-reminder-app

# ```

# 

# 3\. Install all dependencies

# 

# ```bash

# flutter pub get

# ```

# 

# 4\. Run the app on your connected device

# 

# ```bash

# flutter run

# ```

# 

# \### Build Release APK

# 

# ```bash

# flutter build apk --release

# ```

# 

# The APK will be located at:

# build/app/outputs/flutter-apk/app-release.apk

# 

# \---

# 

# \## 🛠️ Tech Stack

# 

# | Technology | Version | Purpose |

# |------------|---------|---------|

# | Flutter | 3.7.0+ | Cross-platform UI framework |

# | Dart | 3.7.0+ | Programming language |

# | Provider | 6.1.5 | State management |

# | Shared Preferences | 2.5.3 | Local data storage |

# | Flutter Local Notifications | 19.2.1 | Push notification scheduling |

# | Table Calendar | 3.2.0 | Calendar widget |

# | Permission Handler | 12.0.0 | Runtime permissions |

# | Timezone | 0.10.1 | Accurate notification scheduling |

# | Google Fonts | Latest | Beautiful typography |

# | Android Intent Plus | 5.3.0 | Android system intents |

# | Badges | 3.1.2 | UI badge components |

# | Font Awesome Flutter | 10.8.0 | Icon library |

# 

# \---

# 

# \## 📁 Project Structure

# lib/

# ├── main.dart                       # App entry point and initialization

# ├── firebase\_options.dart           # Placeholder (Firebase removed)

# ├── secrets.dart                    # Placeholder (no secrets needed)

# │

# ├── models/

# │   ├── event.dart                  # Event data model with JSON serialization

# │   └── app\_user.dart               # User data model

# │

# ├── providers/

# │   ├── theme\_provider.dart         # Theme state management (light/dark)

# │   └── user\_provider.dart          # User state management

# │

# ├── screens/

# │   ├── auth\_screen.dart            # Local login screen (name + email)

# │   ├── on\_boarding\_screen.dart     # App introduction and onboarding

# │   ├── upcoming\_events\_screen.dart # Main screen with tabbed event list

# │   ├── create\_event\_screen.dart    # Form to create new events

# │   ├── edit\_event\_screen.dart      # Form to edit existing events

# │   ├── calender\_screen.dart        # Calendar view with event markers

# │   └── settings.dart               # Settings page

# │

# ├── services/

# │   ├── event\_storage\_service.dart  # CRUD operations using local storage

# │   └── notification\_services.dart  # Notification scheduling and management

# │

# └── widgets/

# ├── appbar.dart                 # Custom reusable app bar

# ├── bottom\_nav\_bar.dart         # Bottom navigation bar

# └── build\_event\_card.dart       # Event card widget with actions

# 

# \---

# 

# \## 🔧 Key Implementation Details

# 

# \### Local Storage Architecture

# All events are stored locally using `shared\_preferences` as serialized JSON strings. The `EventStorageService` class provides clean CRUD operations:

# \- `getEvents()` — Load all events from device storage

# \- `addEvent()` — Save a new event

# \- `updateEvent()` — Update an existing event

# \- `deleteEvent()` — Remove an event by ID

# 

# \### Notification System

# Events use `flutter\_local\_notifications` with timezone-aware scheduling via the `timezone` package. Notifications are:

# \- Scheduled at the exact event date and time

# \- Cancelled automatically when events are deleted

# \- Rescheduled when events are edited

# \- Persisted across app restarts

# 

# \### Offline First Design

# This app is designed to work completely without internet. All data lives on the device. No Firebase, no Google Sign-In, no cloud sync, no API calls.

# 

# \### Theme System

# Dark and light modes are managed by `ThemeProvider` using Flutter's `ThemeMode`. User preference is persisted across sessions.

# 

# \---

# 

# \## 📋 How to Use

# 

# \### Creating Your First Event

# 1\. Launch the app and enter your name on the login screen

# 2\. Complete the onboarding (first launch only)

# 3\. Tap the \*\*+\*\* floating action button on the home screen

# 4\. Fill in the event title (required)

# 5\. Add location, description (optional)

# 6\. Pick a date using the date picker

# 7\. Pick a time using the time picker

# 8\. Toggle notifications ON if you want a reminder

# 9\. Tap \*\*Create Event\*\*

# 

# \### Viewing Events

# \- \*\*All tab\*\* — shows every event you have created

# \- \*\*Upcoming tab\*\* — shows future events only

# \- \*\*Past tab\*\* — shows events that have already passed

# \- \*\*Calendar tab\*\* — tap any date to see events on that day

# 

# \### Managing Events

# \- Tap the \*\*edit icon\*\* on any event card to modify it

# \- Tap the \*\*delete icon\*\* to permanently remove an event

# \- Pull down to refresh the events list

# 

# \### Settings

# \- Toggle \*\*Dark Mode\*\* for a darker interface

# \- Toggle \*\*Notifications\*\* to enable or disable all reminders

# \- Tap \*\*Clear All Events\*\* to delete everything

# \- Tap \*\*Sign Out\*\* to return to the login screen

# 

# \---

# 

# \## 🤝 Contributing

# 

# Contributions are welcome! Here is how you can help:

# 

# 1\. Fork the repository

# 2\. Create your feature branch

# ```bash

# git checkout -b feature/your-feature-name

# ```

# 3\. Make your changes and commit

# ```bash

# git commit -m "Add your feature description"

# ```

# 4\. Push to your branch

# ```bash

# git push origin feature/your-feature-name

# ```

# 5\. Open a Pull Request on GitHub

# 

# \### Ideas for Contributions

# \- Add event categories and color coding

# \- Add recurring events support

# \- Add event search functionality

# \- Add export to calendar feature

# \- Add widget for home screen

# \- Add iOS support and testing

# \- Improve accessibility features

# 

# \---

# 

# \## 🐛 Known Issues

# 

# \- Notifications may not fire if battery optimization is enabled for the app. Go to Settings → Apps → Eventora → Battery → Unrestricted to fix this.

# \- The app currently supports Android only. iOS support is planned.

# 

# \---

# 

# \## 📄 License

# 

# This project is licensed under the MIT License.

# MIT License

# Copyright (c) 2026 Haris Shahzad

# Permission is hereby granted, free of charge, to any person obtaining a copy

# of this software and associated documentation files (the "Software"), to deal

# in the Software without restriction, including without limitation the rights

# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell

# copies of the Software, and to permit persons to whom the Software is

# furnished to do so, subject to the following conditions:

# The above copyright notice and this permission notice shall be included in all

# copies or substantial portions of the Software.

# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR

# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,

# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.

# 

# \---

# 

# \## 👨‍💻 Developer

# 

# \*\*Haris Shahzad\*\*

# 

# \- GitHub: \[@Haris-Shahzad](https://github.com/Haris-Shahzad)

# 

# \---

# 

# \## 🙏 Acknowledgements

# 

# \- \[Flutter](https://flutter.dev) — Amazing cross-platform framework

# \- \[pub.dev](https://pub.dev) — Dart package repository


# 

# \---

# 

# ⭐ If you found this project useful or interesting, please give it a star on GitHub!

# 


# \\---
#
# \\## ✅ Cross-platform compatibility (simple)
#
# This app now runs safely on Android, iOS, and Web with graceful fallback behavior:
#
# \- If Firebase is configured on the platform, cloud features work (Google sign-in, Firestore profile/events, Gemini AI).
# \- If Firebase is not configured on the platform, the app still opens and local/offline flow continues.
#
# \\### iOS (Swift/Xcode) quick setup
#
# 1\. Add your iOS app in Firebase Console with bundle ID.
# 2\. Download `GoogleService-Info.plist`.
# 3\. Place it in `ios/Runner/GoogleService-Info.plist` using Xcode.
# 4\. Run:
#
# ```bash
# flutter run -d ios
# ```
#
# \\### Web quick setup
#
# 1\. Add Web app in Firebase Console.
# 2\. Configure Firebase for web (FlutterFire) for your project.
# 3\. Run:
#
# ```bash
# flutter run -d chrome
# ```
#
# \\### Important note
#
# Event data is now user-scoped:
# \- User A and User B see separate events.
# \- Events are saved under Firestore `users/{uid}/events/{eventId}`.
#
# \\---
#
# \\## 📤 How to share app with friends
#
# For quick sharing (Android APK):
#
# ```bash
# flutter build apk --release
# ```
#
# Share this file:
# `build/app/outputs/flutter-apk/app-release.apk`
#
# For Play Store:
#
# ```bash
# flutter build appbundle --release
# ```
#
# Upload:
# `build/app/outputs/bundle/release/app-release.aab`
#
# eventora-planner
