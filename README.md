# LOS4GM9 — LineageOS 23.2 manifest

This manifest follows the official LineageOS 23.2 manifest and replaces only
`frameworks/base` with `LOS4GM9/android_frameworks_base`, branch `lineage-23.2`.
The public GitHub remote uses HTTPS; cloning public projects needs no SSH key.

## Download the sources

Use a Linux Android build environment with the Android `repo` tool installed:

```sh
mkdir -p los4gm9
cd los4gm9
repo init -u https://github.com/LOS4GM9/android.git -b lineage-23.2 --git-lfs
repo sync -c -j4 --fail-fast
```

## Switch an existing LineageOS 23.2 source checkout

First commit or otherwise preserve any local framework edits. From the Android
source root, change the manifest URL and synchronize:

```sh
repo init -u https://github.com/LOS4GM9/android.git -b lineage-23.2 --git-lfs
repo sync -c -j4 --fail-fast
git -C frameworks/base remote -v
git -C frameworks/base log -1 --oneline
```

Existing local manifests are retained. Remove or update an older local-manifest
override for `frameworks/base` before syncing; it can replace this selection or
create duplicate project paths. Do not use `--force-sync` to hide such a conflict.

GM9 Pro device, kernel and proprietary vendor sources are still supplied by your
existing device setup/local manifests; this repository changes the framework
selection, not the full device dependency set.

## Framework changes

The framework fork shortens six default animation resources in
`core/res/res/values/config.xml`:

| Resource | Upstream milliseconds | LOS4GM9 milliseconds |
| --- | ---: | ---: |
| config_shortAnimTime | 200 | 100 |
| config_mediumAnimTime | 400 | 160 |
| config_longAnimTime | 500 | 240 |
| config_activityShortDur | 150 | 72 |
| config_activityDefaultDur | 220 | 100 |
| config_tooltipAnimTime | 150 | 140 |

Based on droid-legacy changes `e2613c4852a9` and `70316dfd1955`, retaining only
animation resource timings. Input timeouts, scrolling physics, CPU governors,
SurfaceFlinger properties and diagnostic reporting are not modified.
Device overlays may override these resources. These values affect animations
that read them; Launcher-specific and gesture-driven QS transitions have their
own paths. No jank reduction or successful ROM build is claimed by this change.

Both repositories retain their upstream Git history and use `lineage-23.2`.
