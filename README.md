# Remote kill-switch for the Kutta Anime app

The Android app checks this folder's `app-config.json` at every startup.
The file must be pushed to:

    https://github.com/arafat01rahman/version-control-anime-app.git

The app fetches the RAW url:

    https://raw.githubusercontent.com/arafat01rahman/version-control-anime-app/main/app-config.json

(If your default branch is `master` instead of `main`, update `CONFIG_URL`
in `android-app/app/src/main/java/com/kutta/anime/MainActivity.java`.)

## How to control the app

Edit `app-config.json` on github.com and commit. Takes effect the next
time anyone launches the app (while they have internet).

### Shut this version off (kill switch)

```json
{
  "enabled": false,
  "minVersion": 1,
  "message": "This version of Kutta Anime has been discontinued. Please contact the developer."
}
```

Every user sees a full-screen "outdated — contact the developer" notice.
Nobody can use the app until you flip it back.

### Force everyone onto a newer APK (update wall)

Bump `versionCode` in `android-app/app/build.gradle` when you release a new
APK, then raise `minVersion` here:

```json
{ "enabled": true, "minVersion": 2, "message": "..." }
```

Old builds (versionCode < minVersion) are blocked with the message;
the new APK (versionCode >= minVersion) keeps working.

### Re-enable / go back to normal

```json
{ "enabled": true, "minVersion": 1, "message": "..." }
```

## Behavior notes

- **Fail-open:** if GitHub is unreachable (no internet, GitHub down), the
  app works normally. It never bricks users because of a network blip.
- **Sticky block:** once a phone has seen `blocked = true`, that state is
  cached in SharedPreferences — the user stays locked out even if they
  later go offline. The app re-checks on every launch, so if you re-enable
  the app, blocked phones recover on their next successful online launch.
- The check runs BEFORE the WebView loads anything, so a blocked user
  never reaches the streaming UI.
