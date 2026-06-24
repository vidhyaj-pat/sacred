# Sacred Pages — Android (Capacitor)

A native Android wrapper for the Sacred Pages reading app, generated with
Capacitor. This folder is an **Android Studio project** — opening it and
building produces the installable `.apk`. It is not a pre-built APK.

## What this is

The native app loads your live web app (`https://soul-reads.lovable.app`)
inside a full-screen native WebView — no browser chrome, your own launcher
icon, your own splash screen. Because the app is local-first (data lives in
the device's IndexedDB), your reading sessions stay on the phone.

- **App name:** Sacred Pages
- **Application ID:** `app.sacredpages.reader`
- **Launcher icon / splash:** generated from your `icon-512.png`

## Build the APK

You need **Android Studio** (which bundles the Android SDK + Gradle) or a
local JDK 17 + Android SDK.

### Option A — Android Studio (easiest)
1. `npm install`        (restores Capacitor tooling)
2. `npx cap open android`   (opens the project in Android Studio)
3. In Android Studio: **Build → Build App Bundle(s) / APK(s) → Build APK(s)**.
4. The APK lands in `android/app/build/outputs/apk/debug/app-debug.apk`.

### Option B — command line
From this folder, with the Android SDK installed and `ANDROID_HOME` set:
```
npm install
cd android
./gradlew assembleDebug
```
Output: `android/app/build/outputs/apk/debug/app-debug.apk`

Install on a connected device with: `adb install app-debug.apk`

## Offline behavior

First launch needs network (it fetches the app shell from the live URL).
To make it cold-start offline, deploy the **service worker** in your web
project (the `public/sw.js` + registration in `__root.tsx` from earlier).
Once that's live, the WebView caches the shell and the app opens offline.

## Going fully self-contained (no hosted URL)

To bundle the app inside the APK instead of loading the live site:
1. Produce a static build of the web app into a folder (e.g. `www/`).
2. Remove the `server.url` block from `capacitor.config.json`.
3. Point `webDir` at your static build folder.
4. Run `npx cap sync android`, then rebuild.
Note: the web app currently uses TanStack Start (server-rendered), so this
requires configuring a client-only/static build first — a larger change.

## Releasing on Google Play

A debug APK is for testing/sideloading. For the Play Store, build a signed
**release** bundle (`./gradlew bundleRelease`) with your own keystore, and
**keep that keystore safe** — you need the same key for every future update.

## Changing the app icon later
Replace `assets/icon-only.png` (1024×1024) and run:
`npx @capacitor/assets generate --android`
