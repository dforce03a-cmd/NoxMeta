# NoxMeta — Native Android app (Kotlin + Jetpack Compose)

Full native client for the NoxMeta backend (same database, same accounts as the web app).
Open the folder in Android Studio and press **Build > Build APK(s)** — no extra setup.

## Requirements
- Android Studio Ladybug (2024.2) or newer
- JDK 17 (bundled with Android Studio)
- Android SDK 35 (Studio offers to install it on first open)

## Build steps
1. Unzip and open the `noxmeta-android` folder in Android Studio (**Open**, not Import).
2. Wait for Gradle sync (downloads Gradle 8.9 through the wrapper).
3. **Build > Build APK(s)** → APK lands in `app/build/outputs/apk/debug/app-debug.apk`.
4. Or run on a device with the green ▶ button.

### Command line
```bash
chmod +x gradlew        # macOS / Linux only, once
./gradlew assembleDebug           # debug APK
./gradlew assembleRelease         # unsigned release APK
```

### Build in the cloud (no PC needed)
Push this folder to GitHub — `.github/workflows/android.yml` builds the APK on every push
and uploads it as a downloadable artifact.

## What's inside
```
app/src/main/java/app/noxmeta/android/
├── MainActivity.kt              entry point + runtime permissions
├── data/    Supa.kt Models.kt Repo.kt Media.kt      backend, schema, queries, signed URLs
├── call/    CallManager.kt      WebRTC voice/video (same signalling as the web app)
└── ui/
    ├── App.kt                   navigation + bottom bar + call overlay
    ├── theme/Theme.kt           dark NoxMeta theme
    ├── components/              Avatar, PostCard, empty/loading states
    └── screens/                 Auth, Home, Explore, Upload, Reels, Messages, Chat,
                                 Comments, Notifications, Profile, Friends, Archive,
                                 StoryViewer, Settings, Admin, Call
```

## Features
- Username + password sign-up / sign-in (no e-mail verification, like the web app)
- Feed, likes, comments, delete own post, pinned posts
- Stories: view full-screen, post a 24h story, archive of expired stories
- Reels (vertical video pager with ExoPlayer)
- Explore / user search, profiles, follow / unfollow, follower counts
- Direct messages with read receipts, friends (mutual follows) list
- Notifications
- Voice & video calls over WebRTC, interoperable with the web app
- Settings (edit profile, sign out) and the Admin panel for admins

## Calling notes
`CallManager` broadcasts on Supabase Realtime channel `call:<userId>` with the events
`offer`, `answer`, `ice`, `end` — byte-for-byte the same protocol the web client uses,
so phone ↔ browser calls work. Black screens are avoided by sharing one `EglBase`
between the factory and both renderers, attaching remote tracks in `onTrack`, and
queueing ICE candidates until the remote description is set.

## Backend
URL and publishable key live in `data/Supa.kt`. They are public by design; Row Level
Security on the server decides what each signed-in user can read and write.
