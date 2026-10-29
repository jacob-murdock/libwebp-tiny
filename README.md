# libwebp-tiny

Build recipes for a decode-only [libwebp](https://chromium.googlesource.com/webm/libwebp) static library.

This repo holds a build script and a GitHub Actions matrix. It does not contain libwebp itself; the build clones upstream libwebp **v1.5.0** into `src/` and builds only the `webpdecoder` target. The decoder is about 200–300 KB of code, and the advanced decode API (`use_scaling`) can decode straight to a smaller output size.

## What's in a build

Produced: `libwebpdecoder.a` plus the public headers `webp/decode.h` and `webp/types.h`.

Left out: the encoder, mux/demux, animation utilities, extras and all CLI tools (`cwebp`, `dwebp`, `gif2webp`, `img2webp`, `vwebp`, `webpinfo`, `webpmux`). SIMD and threading are on.

Upstream has no switch to drop the encoder from `libwebp`, so the script builds only the `webpdecoder` target. The full CMake flag list is in `scripts/build.sh`.

## Building

Requirements: CMake, make and a C toolchain.

```bash
make fetch    # clones libwebp v1.5.0 into src/
make build    # native host -> out/linux/ (or out/macos/)
```

Cross builds are selected with `TARGET_PLATFORM`:

```bash
make build TARGET_PLATFORM=windows     # MinGW from Linux
TARGET_PLATFORM=android make build     # needs ANDROID_NDK_HOME or ANDROID_NDK_ROOT
```

Supported values: `linux`, `windows`, `macos` (arm64), `macos-x86_64` (Intel, cross-compiled on Apple Silicon), `ios`, `ios-simulator` (arm64), `android` (arm64-v8a), `android-x86_64`.

Output goes to `out/<platform>/lib/libwebpdecoder.a` and `out/<platform>/include/webp/`. `make clean` removes `out/` and the build trees. Override the upstream tag with `WEBP_TAG=...`.

## CI

`.github/workflows/build-libwebp.yaml` runs on manual dispatch and builds for `linux`, `windows`, `macos`, `macos-x86_64`, `ios`, `ios-simulator`, `android` and `android-x86_64`. Each artifact (`libwebp-tiny-<target>`) contains `lib/` and `include/`.

## Licensing

libwebp is BSD-3-Clause. If you redistribute the built library, include upstream's `COPYING` notice (and `PATENTS` grant) with your app.

The build scripts, configs and CI workflows in this repo are MIT licensed (see `LICENSE`). That license doesn't cover the libwebp source fetched into `src/` or the libraries built from it, which stay under libwebp's BSD-3-Clause license.
