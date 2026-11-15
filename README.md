# Capture: Android screen recorder (work in progress)

## Build and install
1. Install Android Studio (Ladybug or newer) with Android SDK 35.
2. File > Open > select this folder. Let Gradle sync (it downloads Gradle 8.9 itself).
3. Plug in a phone (Android 10+, USB debugging on) and press Run. Or: `./gradlew assembleDebug` and install `app/build/outputs/apk/debug/app-debug.apk`.
4. Unit tests: `./gradlew test`

## What exists in this drop
| Area | Status |
|---|---|
| Gradle project, manifest, permissions, foreground service types | written |
| Capability detection (encoders, size/fps/codec checks, hardware flag) | written |
| MediaProjection > VirtualDisplay > hardware encoder > MP4 (H.264/HEVC, VBR) | written |
| Internal audio + mic capture, mixer, AAC, shared A/V clock, live gains | written |
| Pause/resume (frame gating, timestamp offset, forced keyframe) | written |
| Countdown, notification controls, storage guard, save to Movies/ScreenRecorder | written |
| Home screen with Record and settings chips | written |
| Unit tests for size/bitrate math | written |

None of this has been compiled or run on a device yet. Expect to fix a few compile errors on first build, and send them back.

## Not built yet
Library, editor, remaining 8 screens, floating controls, facecam, drawing, touch indicators (Developer Options deep link),
mid-recording rotation, segmented crash-safe files, thermal monitoring, call interruption handling, iOS.

## Known platform limits
- Internal audio: Android 10+, only apps that allow capture. Silent for opted-out apps and DRM. The app warns after 8 s of silence.
- Device audio also needs the microphone permission (same AudioRecord API).
- Protected (FLAG_SECURE) content records black.
- Output resolution is fixed at start from the screen orientation at that moment.
- Recording is lossy (H.264/HEVC). Nothing here is lossless.

## Test checklist (for you)
1. 1080p/30, mic only: play back, check sound and picture.
2. Device audio only, with YouTube or a game: sound present? Try a game that blocks capture: does the warning appear?
3. Device + mic with headphones, then without: echo?
4. 60 fps: check smoothness; run `adb shell dumpsys media.codec` if frames drop.
5. Pause/resume 3 times: audio and video stay in sync, no green frames.
6. Rotate phone before recording, then record in each orientation.
7. Fill storage near 200 MB: recording should stop and still save.
8. Notification Stop, and the system "Stop sharing" chip: file must still play.
9. 10+ minute recording.

## Install without Android Studio (GitHub builds the APK for you)
1. Create a free account at github.com, then a new empty repository (private is fine).
2. Upload all files from this folder, including the hidden `.github` folder (easiest: install GitHub Desktop, or use "uploading an existing file" and drag the whole unzipped folder).
3. Open the repository's Actions tab. The "Build APK" run starts automatically (about 5 minutes).
4. Open the finished run, download the "Capture-debug-apk" artifact, unzip it, and send `app-debug.apk` to your phone.
5. On the phone, open the APK and allow "Install unknown apps" when asked.
If the run fails, open the failed step and send me the red error lines.
