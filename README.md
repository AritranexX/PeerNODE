# PeerNODE

Serverless P2P messaging app (Flutter). Identity-first: no accounts, no servers.

## What works today
- **Identity**: random Ed25519 key pair generated on first launch; the private seed lives only in Android Keystore-backed secure storage.
- **Contacts via QR / deep link**: `peernode://chat?key=<publicKey>` links and QR codes (scanner uses `mobile_scanner`, links use `app_links`). Input is validated (scheme, host, 32-byte key); own links and duplicate deliveries are handled.
- **Local chat storage**: Drift/SQLite database; message bodies are encrypted at rest with AES-256-GCM (random key in secure storage).
- Settings: show/copy your invite link and key fingerprint, delete messages older than 30 days.
- Startup is guarded: a failed storage/identity/database open shows a recoverable error screen.

## Not implemented yet
Peer-to-peer **delivery** (nearby_connections / WebRTC), group joins, polls UI, media pipeline, foreground service. Those packages were removed from `pubspec.yaml` until the code that uses them exists. Chats currently store messages on-device only.

## Build
```bash
flutter pub get
flutter analyze && flutter test
flutter build apk --release
# -> build/app/outputs/flutter-apk/app-release.apk
```

### Release signing
Create `android/key.properties` (git-ignored):
```
storeFile=app/upload-keystore.jks
storePassword=...
keyAlias=upload
keyPassword=...
```
Without it the release APK is signed with the debug key (a warning is printed) so it still builds, but it must not be distributed.

### Android Gradle notes
AGP 9 is used with `android.newDsl=false` and `android.builtInKotlin=false` (`android/gradle.properties`) because many Flutter plugins still rely on the legacy DSL. With built-in Kotlin off, `android/app/build.gradle.kts` applies `org.jetbrains.kotlin.android` itself.

### Before publishing
Change `applicationId`/`namespace` from `com.example.peernode` (rejected by Google Play).
# PeerNODE
