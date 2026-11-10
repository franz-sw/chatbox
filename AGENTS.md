# Chatbox Android Development Guide

This repository contains the Chatbox codebase. This guide focuses on developing and building the **Android** version.

---

## Architecture Overview

Chatbox uses a shared React frontend packaged for Android via **Capacitor 7**:

```
[src/renderer/] ──(Vite build)──> [release/app/dist/renderer/] ──(Capacitor sync)──> [android/app/src/main/assets/public/] ──(Gradle)──> APK
```

- **Frontend & UI**: Located in `src/renderer/` using React 18, TanStack Router, Mantine, and Tailwind CSS.
- **Packages**:
  - `packages/chatbox-core`: Business logic, session management, storage schemas.
  - `packages/chatbox-react`: React hooks, context, and query layer.
  - `packages/chatbox-i18n`: Internationalization translations.
- **Platform Layer**: Located in `src/renderer/platform/`. Mobile uses `MobilePlatform` (`mobile_platform.ts`) and SQLite via `@capacitor-community/sqlite`.
- **Android Native Project**: Located in `android/`. Standard Gradle Android project generated and managed via `@capacitor/android`.

---

## Environment & Prerequisites

- **Node.js**: v22.x
- **Package Manager**: `pnpm` (v10.33.0)
- **Java**: OpenJDK 21 (`JAVA_HOME = C:\Users\Roland\scoop\apps\openjdk\current`)
- **Android SDK**: `ANDROID_HOME = C:\Users\Roland\AppData\Local\Android\Sdk`
  - Compile SDK: `35` (Android 15)
  - Target SDK: `35`
  - Min SDK: `23` (Android 6.0)

---

## Key Commands

| Task | Command | Description |
|---|---|---|
| **Build Android APK** | `pnpm run android:build` | Compiles web renderer, syncs to `android/`, and runs `./gradlew assembleDebug` |
| **Sync Web to Android** | `pnpm run android:sync` | Compiles web renderer with mobile env flags and syncs to `android/` |
| **Compile Mobile Web Only** | `pnpm run build:mobile:renderer` | Runs Vite build with `CHATBOX_BUILD_TARGET=mobile_app CHATBOX_BUILD_PLATFORM=android` |
| **Regenerate App Icons** | `pnpm run mobile:assets` | Regenerates adaptive icons and splash screens from `resources/` into `android/` |
| **Open in Android Studio** | `pnpm run android:open` | Launches Android Studio with the `android/` project |
| **Typecheck** | `pnpm run check` | Checks TypeScript across the project |

---

## Android Build Outputs

- **Debug APK**: `android/app/build/outputs/apk/debug/app-debug.apk`
- **Web Assets in Android**: `android/app/src/main/assets/public/`

---

## Development Guidelines

1. **Source of Truth for UI**:
   - Always make frontend edits in `src/renderer/` (or `packages/`).
   - Never manually edit `android/app/src/main/assets/public/` — it is overwritten on each `pnpm run android:sync`.

2. **Platform Checks**:
   - Check mobile platform via `CHATBOX_BUILD_TARGET === 'mobile_app'` and `CHATBOX_BUILD_PLATFORM === 'android'`.
   - Mobile platform adapter is initialized in `src/renderer/platform/index.ts`.

3. **Capacitor Configuration**:
   - Root configuration is defined in `capacitor.config.ts`.
   - App ID: `xyz.chatboxapp.ce`
   - App Name: `Chatbox`
   - Web directory: `release/app/dist/renderer`
