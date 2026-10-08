# Solid ripple and O3 source review

Reviewed on 2026-10-08. The scan covers every tracked `.bp` and `.mk` file in
each snapshot below, plus the previously archived droid-legacy 19.1 patches.
This is an inventory of explicit `-O3` text, not a determination of the final
compiler flags after Soong defaults, toolchain flags, AFDO and LTO processing.

| Repository / snapshot | SHA | Build files | Explicit O3 |
| --- | --- | ---: | --- |
| droid-legacy 19.1 | e36a456106089061e2e22354037c828b1e605cc1 | 719 | HWUI, core JNI, bootanimation, five tools groups |
| droid-legacy 23.2 | 69d335d01db253043536ae334ff07ce2646c2a94 | 1016 | HWUI only |
| LineageOS-UL default branch 21.0 | aca8bb27401d94ac4521043e13f414e19f51bd3c | 869 | None |
| LineageOS-UL linked EdgeEffect commit | a27f4f50759e67eb46fecd3126c4e704485c9c2b | 926 | None |

## Where 19.1 uses O3

- `libs/hwui/Android.bp`: HWUI defaults, the old performance defaults, graphics
  APEX/JNI groups and an Android target subsection. Several entries converge
  on the same library; five occurrences do not mean five cumulative optimizations.
- `core/jni/Android.bp`: libandroid_runtime C/C++ flags. A single `cflags`
  setting can cover C and C++; duplicating it in `cppflags` is unnecessary.
- `cmds/bootanimation/Android.bp`: boot animation, not normal shade rendering.
- `tools/aapt2`, `tools/bit`, `tools/split-select`, `tools/streaming_proto`,
  `tools/validatekeymaps`: resource/development tools, not a general SystemUI fix.

The aapt2 patch also adds `-march=native` and `-mtune=native`; these are not
carried into Android device builds. Old HWUI `-marm` / `-mapcs` settings are
also not enabled.

Source patches:

- [HWUI 19.1](https://github.com/droid-legacy/android_frameworks_base/commit/d62ea9fdefb758388b949bca2f544cbbb1c22a10)
- [Core JNI](https://github.com/droid-legacy/android_frameworks_base/commit/e3fe713fa70b9288725a8f049e12dde31b7dcccb)
- [Tools](https://github.com/droid-legacy/android_frameworks_base/commit/2b2cd5c0ea7c71fbc724c30b4268f5ef0de23bfb)
- [Boot animation](https://github.com/droid-legacy/android_frameworks_base/commit/47e96fc87e477c6d71c57135dabff418b60d587c)
- [HWUI 23.2 O3](https://github.com/droid-legacy/android_frameworks_base/commit/516f89448309)

## LOS4GM9 adaptation

1. `RippleDrawable`: change the forced default from patterned to solid. Existing
   explicit styles and independent Compose ripple implementations are not changed.
2. `libs/hwui/Android.bp`: add `-O3` in `hwui_defaults.target.android.cflags`.
   Both libhwui and libhwui_static inherit this. The libhwui graphics JNI/APEX
   source groups share the resulting library compilation flags, so redundant
   O3 entries in those defaults are unnecessary.
3. `core/jni/Android.bp`: add `-O3` to
   `libandroid_runtime_defaults.target.android.cflags`.

Host compiler settings, frame-pointer choices, ThinLTO and AFDO settings are
unchanged. The dormant `hwui_compile_for_perf` group remains disabled.
Bootanimation and tool optimizations are not included. Window corner settings
are unchanged.

## Validation limits

The changed properties are existing Soong target/cflags structures; patches
were reviewed and passed `git diff --check`. Remote commits are verified after
push. This workspace has no complete Android build environment: the changes
have not passed a full Soong build or an on-device jank comparison. Check the
effective compile commands for libhwui and libandroid_runtime after building.
O3 may increase code size and is not a guaranteed speedup.
