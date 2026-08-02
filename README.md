# MoppChem — Android APK build

This folder is a complete **Gradle Android project** that wraps the single-file
MoppChem web app in a WebView and builds it into an installable **APK** using
**GitHub Actions** (no local Android Studio needed).

## How it works
- `app.html` (repo root) is the **single source of truth** — the exact same file
  as `moppchem/app.html` (the fixed, robust app).
- The GitHub Actions workflow:
  1. copies `app.html` into `app/src/main/assets/www/`,
  2. sets up JDK 17 + Android SDK,
  3. builds the debug APK with Gradle,
  4. uploads the APK as a build artifact you download and install.

## First-time setup (one push)
1. Create a GitHub repo and push this folder to it (`main` or `master`).
2. In GitHub, open the **Actions** tab → the build runs automatically.
3. When it finishes, open the run → scroll to **Artifacts** → download
   `MoppChem-debug-apk.zip` → extract → install `app-debug.apk` on your phone
   (enable "install from unknown sources").
4. You can also press **Run workflow** in the Actions tab to rebuild anytime.

## Editing the app later
Just edit `app.html` and push. The workflow restages it and produces a fresh
APK. Nothing else needs to change.

## What the WebView does
- Loads `file:///android_asset/www/app.html` fullscreen.
- Enables JavaScript + `localStorage` (so your offline data persists).
- Grants `INTERNET` permission so the app can reach your Google Apps Script URL.
- Your spreadsheet sync URL is set in-app under **Settings → Google Sheets
  Integration** (the same way as the browser version).

## Gotchas
- **Printing / PDF** uses `window.print()`, which has limited support inside a
  WebView — use the browser version if you need the receipt/report printouts.
- For a **release** (signed) APK you would add a keystore + signing config and
  switch the workflow to `assembleRelease`. The debug APK is fine for personal use.

## Folder layout
```
app.html                              # the app (edit this)
.github/workflows/build-apk.yml       # CI build
app/build.gradle                      # module config
app/src/main/AndroidManifest.xml      # app manifest + INTERNET permission
app/src/main/java/.../MainActivity.java  # WebView wrapper
app/src/main/assets/www/app.html      # staged app (auto-copied by CI)
app/src/main/res/...                  # icon + theme
```
