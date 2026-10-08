# Legacy kernel / BPF patch integration

Patch source: https://github.com/techyminati/fuck-bpf/tree/990553ae10efee32d5915c7f098b81a87e76771f

Imported all 29 mail patches from lineage-23.2 into nine LOS4GM9 forks. Original author names, email addresses, author timestamps, commit messages and trailers are retained. Patch IDs were compared individually and match. Commit IDs differ because the parent and committer differ.

DnsResolver and system/apex are based on the manifest's original AOSP android-16.0.0_r4 tag; other projects use the LineageOS lineage-23.2 branch tips recorded below. GitHub fork relationships point to the LineageOS mirrors; the AOSP tag ancestry is retained for these two branches.

| Source path | Fork branch | Base | Patched head | Count |
|---|---|---|---|---|
| `frameworks/native` | [LOS4GM9/android_frameworks_native](https://github.com/LOS4GM9/android_frameworks_native/tree/lineage-23.2) | `9568531f9be7ae65f260699811f32ec4e461de6e` | `735a013c9ec4806cdc811a431a19e261834b1e59` | 1 |
| `hardware/interfaces` | [LOS4GM9/android_hardware_interfaces](https://github.com/LOS4GM9/android_hardware_interfaces/tree/lineage-23.2) | `13d687a84c834af855bfb314b853b8372a7aa6d9` | `e178626cc75ab3266f906eab287ed824f05dbf50` | 1 |
| `kernel/configs` | [LOS4GM9/android_kernel_configs](https://github.com/LOS4GM9/android_kernel_configs/tree/lineage-23.2) | `091688a5c5186cd124a0d19b06441bb8b04c3073` | `cbc9d7083bc0d138384006416f07746867575ae7` | 7 |
| `packages/modules/Connectivity` | [LOS4GM9/android_packages_modules_Connectivity](https://github.com/LOS4GM9/android_packages_modules_Connectivity/tree/lineage-23.2) | `c9f3e7795256bd4a3f99d02e604e24fd1552c2b3` | `527868f6e15c75f9387aae83296ed4338ddf16ef` | 12 |
| `packages/modules/DnsResolver` | [LOS4GM9/android_packages_modules_DnsResolver](https://github.com/LOS4GM9/android_packages_modules_DnsResolver/tree/lineage-23.2) | `278cc346b4a2afd88e9b1155b716ebcb50dff8ca` | `a64f173e38e73785dd150e1e639680d3c6154975` | 1 |
| `system/apex` | [LOS4GM9/android_system_apex](https://github.com/LOS4GM9/android_system_apex/tree/lineage-23.2) | `97548ed51112062a4d1762a7dffa0cadcc09bda9` | `8195706f0e8767f92f0559cdb2c4ba489262e536` | 1 |
| `system/bpf` | [LOS4GM9/android_system_bpf](https://github.com/LOS4GM9/android_system_bpf/tree/lineage-23.2) | `6c5f4f865220e27cda2a9f4210023a7a10f5bafc` | `3ad4bc4fafe98f7e4b9bc3d661210fcccb447d5e` | 3 |
| `system/core` | [LOS4GM9/android_system_core](https://github.com/LOS4GM9/android_system_core/tree/lineage-23.2) | `eb2de7321317226bbc1951382b3171fa59bb4d1d` | `c7c0a8f3d01bfd5cf7af8f0f7c1f49082d4ec2ae` | 1 |
| `system/netd` | [LOS4GM9/android_system_netd](https://github.com/LOS4GM9/android_system_netd/tree/lineage-23.2) | `796eaca5ab9dfdd87751c51bb71fa3f26c87d034` | `f4ea03ff5e8d089d6cb6625bdb06b7db3f2c188a` | 2 |

## Validation and limitations

All repositories have clean worktrees; all 29 patch IDs and author metadata were verified; remote branch heads were verified after pushing. Manifest XML parses and each target is selected exactly once. Original whitespace warnings in kernel/configs and system/bpf were preserved rather than rewriting upstream patches.

No full Android build, boot, CTS/VTS, or network validation was performed. This is a compatibility patchset, not a measured performance improvement. It relaxes kernel/BPF checks and tolerates initialization failure; this does not supply missing kernel BPF functionality. Validate Wi-Fi, cellular data, DNS, IPv6/CLAT, VPN, tethering, data accounting and per-UID network restrictions on the resulting ROM.

The BLAST patch falls back from epoll_pwait2 to epoll_wait on ENOSYS. The APEX patch avoids O_DIRECT on kernels <=4.19, with possible RAM/performance cost. No phone, device-tree or actual device-kernel repository was changed.

## Sync

```sh
repo init -u https://github.com/LOS4GM9/android.git -b lineage-23.2 --git-lfs
repo sync -c -j4 --fail-fast
```
