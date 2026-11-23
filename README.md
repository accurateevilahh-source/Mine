# Minecraft LCE for Android

An independent Android porting workspace based on Portable LCE and the 4JCraft Android launcher.

## Project goal

Build a native ARM64 Android APK that runs Minecraft: Legacy Console Edition using the Portable LCE engine and an Android launcher.

## Upstream projects

- Engine: https://github.com/portable-lce/portable-lce
- Android launcher: https://github.com/ItzVladik/4jcraft-laucnher

This repository is the integration workspace. It does not contain the upstream game engine or game assets yet.

## Current status

- [x] Create the Android port workspace
- [ ] Inspect and pin compatible upstream revisions
- [x] Draft Android Meson/filesystem and EGL renderer recovery overlays (compile validation pending)\n- [ ] Apply and validate the Android platform backend against current Portable LCE
- [ ] Build `libMinecraftClient.so` for `arm64-v8a`
- [ ] Integrate the 4JCraft launcher
- [ ] Add/import legally obtained game data
- [ ] Test on a physical Android device
- [ ] Publish a tested APK in GitHub Releases

## Important

The Portable LCE README says its Android port predates major refactors. The current main branch's documented build targets are desktop, so simply compiling the current main branch will not produce the Android library expected by the launcher. The Android backend must be reconciled first.

No APK is available yet. Do not treat a successful desktop build or launcher-only APK as a completed game port.

## Build target

- Android ABI: `arm64-v8a`
- Native engine library expected by launcher: `libMinecraftClient.so`
- Output goal: signed installable APK
