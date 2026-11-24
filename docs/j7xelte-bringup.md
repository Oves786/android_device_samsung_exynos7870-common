# j7xelte modern Android bring-up

**Status: experimental source migration, not buildable or flashable yet.** Target device: Galaxy J7 (2016), Exynos variant j7xelte. Android 17 is the eventual goal, not a claim of support.

## Baseline and source references

- Original common tree: `lineage-15.1`, 367b878e70bc0b3f689bbd4ad1ed6ba83776ef39. Original branches must remain unchanged.
- Original device-specific tree: [Oves786/android_device_samsung_j7xelte](https://github.com/Oves786/android_device_samsung_j7xelte/tree/lineage-15.1), d23d57de840bae0514d6c7fc8bfadd5b4df725e8. Original vendor and kernel trees: [vendor](https://github.com/Oves786/android_vendor_samsung_j7xelte/tree/lineage15.1), [kernel](https://github.com/Oves786/android_kernel_samsung_j7xelte/tree/O-8.1).
- Android 12L device-specific reference: [Exynos7870-Revived-Beta/j7xelte](https://github.com/Exynos7870-Revived-Beta/android_device_samsung_j7xelte/tree/3f6b32c22ce9195ad45c417f412703b5f5a1c8c2), [common](https://github.com/Exynos7870-Revived-Beta/android_device_samsung_universal7870-common/tree/lineage-19.1), [kernel](https://github.com/Exynos7870-Revived-Beta/android_kernel_samsung_exynos7870/tree/lineage-19.1), [vendor](https://github.com/Exynos7870-Revived-Beta/android_vendor_samsung_j7xelte/tree/lineage-19.1). Verify full dependency set and licenses before importing.
- Android 15 family reference: [Exynos7870-labs playground](https://github.com/Exynos7870-labs/android_device_samsung_playground/tree/1728c108c42efc94b6d9d8c3ec229c793d90df8f). This contains a3y17lte and universal7870-common sources, not a confirmed Android 15 j7xelte port. [Releases](https://github.com/Exynos7870-labs/lineage_release/releases) report legacy-kernel work and limitations (including VoLTE and FBE). Do not flash another model's image or copy partition sizes.
- Final platform: [Android 17 AOSP release notes](https://source.android.com/docs/whatsnew/android-17-release).

## Checkpoint 1 — architecture

- [x] Separate bring-up branch from the historical common tree.
- [x] Stage ARM64 + 32-bit multilib userspace for TARGET_DEVICE=j7xelte, based on the Android 12L common tree architecture; retain historical architecture settings for other targets.
- [x] Align kernel header architecture with selected userspace.
- [ ] Verify on a complete build tree; legacy 32-bit-only Mali/OMX, RIL and camera components may need 32-bit processes or wrappers. Existing BSP dependencies are still Oreo-era.

## Checkpoint 2 — device-specific Android 12L baseline

- [ ] Reconcile common path exynos7870-common vs universal7870-common with the j7xelte device tree and dependency manifest. Keep source provenance and original licenses.
- [ ] Pin matching kernel, vendor, common vendor, Samsung SLSI BSP/HALs, framework patches and device-specific repositories. Audit vendor blob ELF architecture and dependencies.
- [ ] Port matching kernel configuration (exynos7870-j7xelte_defconfig), kernel build flags and boot image layout. Keep original boot offsets until verified against j7xelte. Do not blindly replace the bootloader or use a generic GKI image.
- [ ] Verify original hardware model, partition table and image size limits against the test device. No repartitioning or flashing before backups and recovery plan.
- [ ] Build recovery and boot image, then full ROM; test early boot via adb/logcat, kernel logs and recovery.
- [ ] Replace legacy init, overlays, HAL registrations, media shims and build modules incrementally. Preserve local changes that remain needed.
- [ ] Remove legacy forced permissive boot flag as part of a coordinated SELinux policy migration; target enforcing. Never represent an insecure engineering build as production-ready.

## Checkpoint 3 — newer Android

- [ ] Adapt Android 15 reference changes selectively for j7xelte, including vendor interface compatibility, graphics, networking/eBPF, kernel ABI/backports and media. Validate against an actual j7xelte build.
- [ ] Evaluate Android 16/17 requirements and whether legacy kernel backports are viable or a new kernel port is necessary. Kernel version alone is not enough; functionality and security are required.
- [ ] Verify calls/SMS/data, Wi-Fi, Bluetooth, audio, camera, sensors, GPS, charging, storage, suspend, thermal behavior, recovery, SELinux and encryption. Document failures honestly.

## Build status

No Android source compilation or device testing has been performed. A complete ROM needs a matching Android source checkout, build machine, and physical j7xelte test device. The changes on this branch are not a flashable release.
