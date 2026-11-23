# Android port integration notes

## Integration boundary

The launcher expects the engine as:

```
android/lib/arm64-v8a/libMinecraftClient.so
```

The launcher repository documents this build sequence:

1. Compile Portable LCE.
2. Copy the resulting `libMinecraftClient.so` into the path above.
3. Configure and build the launcher with its WAF script.
4. Retrieve the generated APK from `build/android/mod-signed.apk`.

## Blocking compatibility issue

Portable LCE's current README describes Android as a work-in-progress port that predates major refactors. Its current documented desktop backend matrix uses SDL2/OpenGL and desktop application targets. The Android backend therefore needs to be recovered/rebased or reimplemented against the current engine interfaces before the launcher can be linked.

## Required validation before release

- Native library is ELF 64-bit AArch64 and loads on Android.
- APK packages the native library under `lib/arm64-v8a/`.
- App launches without missing-symbol or missing-library errors.
- Rendering, audio, touch input, pause/resume, and storage are tested on-device.
- World save/load is verified.
- Release APK installs and starts on a clean ARM64 Android device.

## Game data

Do not commit copyrighted game binaries or assets to this repository. The app should use game data supplied by the user, where legally permitted, and explain any required files clearly.
