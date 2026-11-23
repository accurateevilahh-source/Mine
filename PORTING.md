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


## Android backend recovery investigation (2026-11-23)

A historical Android-support change exists in Portable LCE:

- Initial Android-support commit: `fa6861b22142a2d1a3ba51da24f7d7795cd040ac`
- It was merged by PR #401 and then explicitly reverted by commit `e452788fff8719c2535fa96d6666c91c09016c14` (merge revert `9c0dfd60d51d3886b1822b23c3b12a20ece976c8`).

The old change added an Android shared-library target, GLES/EGL handling, Android logging, and an NDK cross-file. However, it modifies older source paths and build assumptions. It is not safe to cherry-pick onto current main without rebasing and compiling/testing it. The revert is a warning that this historical patch must be treated as a recovery reference, not as a known-good Android build.

### Build result

No Android engine library has been produced. The launcher build requires `android/lib/arm64-v8a/libMinecraftClient.so`; without that engine library, a launcher-only APK would not be a playable port. Do not publish a launcher-only APK as a Minecraft release.

### Next implementation steps

1. Rebase the historical Android/GLES changes against the current engine source layout.
2. Build an AArch64 Android shared library with the NDK and resolve compile/link errors.
3. Package it through the 4JCraft launcher.
4. Test install, launch, rendering, input, audio, and world storage on an Android device before release.


## Current recovery work (2026-11-23)

The first rebaseable recovery overlays are now committed on the `android-engine-recovery` branch:

- `patches/portable-lce-android-recovery.patch`
  - Adds Android to the current Meson platform selection.
  - Builds the application target as `libMinecraftClient.so` on Android, matching the launcher's expected library name.
  - Uses the launcher's `MC_PATH` environment variable for the game-data base directory rather than assuming a desktop executable path.
  - Removes the app-level unconditional GLEW dependency; the renderer backend selects GLES or desktop GL itself.
- `patches/portable-lce-android-egl-renderer.patch`
  - Adapts the current renderer's worker-context setup to EGL pbuffer surfaces on Android, instead of creating hidden SDL windows.
  - Links EGL for the Android GLES renderer.

### How this maps to the launcher

The launcher's `MainActivity2` sets `MC_PATH` from its selected directory before loading `SDL2` and `MinecraftClient`, and its SDL activity resolves the native entry point named `main`. This is why the recovered app target is a shared library named `MinecraftClient`, while the game entry point remains the current desktop main translation unit.

### Validation status

These are source-level recovery overlays, not a completed build. They have not yet been applied to a local checkout or compiled with the Android NDK. In particular, the Android SDL2 dependency/toolchain setup and EGL context lifecycle still need a real ARM64 NDK build and device test. Do not mark the engine recovered until the patch applies cleanly and produces a loadable `libMinecraftClient.so`.
