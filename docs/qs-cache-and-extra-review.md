# QS tile cache and additional legacy commit review

Reviewed on 2026-10-08.

## Applied

[crDroid 119e0e64828c811df308c7d2337c1bc9ceacc73c](https://github.com/crdroidandroid/android_frameworks_base/commit/119e0e64828c811df308c7d2337c1bc9ceacc73c)
was absent from LOS4GM9 and its original mail patch was applied without source
changes, preserving author and Change-Id, as
`4a2c1d30058ba3509f7ad2e78069ebd13245a1bd`.

The QS getter now reuses its list/TileViewModels until the TileModel list
reference changes. QQS reuses its per-spec TileViewModels while recalculating
row/column/width selection. A new source list rebuilds the cache, so it is not an
unbounded permanent spec-only cache. CurrentTilesInteractor constructs a new
resolved list with `.map { TileModel(...) }` before publishing it.

Validation: original patch applicability, changed-file/diff review,
`git diff HEAD^ HEAD --check`, and published branch hash verification. No full
Android/Kotlin build environment or device benchmark was used. Tile editing,
rotation, custom-tile rebinding and user switching still require device tests.

## Research scope

- Revisited the archived 166 droid-legacy 19.1 commits and selected patches.
- Refreshed the latest 100 commit records for droid-legacy 19.1 and 23.2 and
  LineageOS-UL 21.0; screened titles, inspecting relevant diffs rather than
  treating every commit as a performance fix.
- Compared LineageOS-UL 21.0 against official LineageOS 21.0: 27 ahead / 229
  behind at review time, including merges. All 27 ahead titles were screened.
  This comparison uses a common ancestor and is not a direct head-to-head diff.
- This is not a claim to have audited the repositories' entire histories.

## Other candidates, not applied

1. **ThemedResourceCache ArrayMap to HashMap**:
   [01121e087775](https://github.com/droid-legacy/android_frameworks_base/commit/01121e08777594321148286f99d9f08d80a3c19e).
   Relevant to framework themed resource/drawable lookup during View inflation.
   Original profiling cited a small fraction of total CPU time, not a phone-wide
   speedup. Current 23.2 still uses ArrayMap. Modern prune/clear/configuration
   paths need adaptation; HashMap may increase memory overhead. Lower priority
   than the QS cache and deferred trim experiments.
2. **LayoutInflater direct constructors**:
   [13772f7cc7c7](https://github.com/droid-legacy/android_frameworks_base/commit/13772f7cc7c761e7c46846a585d5df9d49c2b6b8).
   Could avoid reflection for some initial View construction. The reviewed patch
   bypasses mFilter on direct-construction success and changes behavior for some
   class names. It needs correctness repairs and tests before adoption; no
   per-frame Compose speedup is established.
3. **Legacy cgroup freezer support**:
   [571443929f98](https://github.com/LineageOS-UL/android_frameworks_base/commit/571443929f98f6d1378f8df9dcbee94811a2ca4c)
   and its preceding revert series restore a cgroup-v1 freezer path. Potentially
   relevant only if GM9 Pro's actual kernel, binder freezer ioctls and mount
   layout support this and the current ROM freezer is not functioning. Not a
   direct QS animation patch. Do not transplant the Android 14 support-detection
   change alone into Android 16.
4. **Cached-process kill timing around boot**:
   [a5c941f932da](https://github.com/LineageOS-UL/android_frameworks_base/commit/a5c941f932da9470cf8669db68a4e4b4bd915707).
   A memory/process-lifetime policy knob, not free performance. It requires
   cold/warm start and memory-pressure measurements; retaining more processes
   can increase pressure and retaining fewer can increase reloads.
5. **Deferred shade trim** remains the most direct next isolated QS experiment:
   [c2a4e06fb335](https://github.com/crdroidandroid/android_frameworks_base/commit/c2a4e06fb335).
   Delay trim after collapse, cancel on reopen, and verify hidden/keyguard state
   before execution. Measure close/reopen frames and retained resources.

## Excluded

The 23.2 media FrameLayout change is already present upstream. The 19.1 blur
wakeup switch is not useful if the device's blur path is unsupported. Davey/log
suppression does not repair missed frames. UL Exynos4/Mali EGL changes, GPS
rollover and old radio compatibility patches are not general SDM660 UI tweaks.
Ignoring cgroup errors or weakening sensor privacy checks is not a jank fix.

Window corner settings remain unchanged. No additional candidate in this
document was bundled with the applied QS cache patch.
