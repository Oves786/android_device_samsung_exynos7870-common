# j7xelte Android 12L reference build

This checkpoint establishes a device-specific baseline before migrating the historical common tree to Android 15/16/17. It is NOT a build of the Oreo-derived common tree and has not been compiled here.

## Prepare a clean Linux build workspace

Install the Android build dependencies and Google's repo launcher using the LineageOS build documentation. Use a dedicated Linux build host with adequate storage and RAM. Do not run these commands on the phone or router.

```bash
mkdir -p ~/android/j7xelte-reference
cd ~/android/j7xelte-reference
repo init -u https://github.com/LineageOS/android.git -b lineage-19.1 --git-lfs
mkdir -p .repo/local_manifests
curl -fL https://raw.githubusercontent.com/Oves786/android_device_samsung_exynos7870-common/3d717beab50ef4a1103ebf7b3eda22021d78c88a/bringup/manifests/j7xelte-lineage19.1-reference.xml \
  -o .repo/local_manifests/j7xelte.xml
repo sync -c --no-clone-bundle --no-tags --optimized-fetch -j4
repo manifest -r -o j7xelte-resolved-manifest.xml
```

The local manifest pins the 13 device-related repositories. The LineageOS base branch itself is not pinned by this manifest; archive the resolved manifest to reproduce the exact synced source. The inspected LineageOS manifest head was cbe1696822ac0165e00c64b89af3dc3190204893 (Android 12.1.0_r22 base).

## Preflight

```bash
test -f device/samsung/j7xelte/AndroidProducts.mk
test -f device/samsung/universal7870-common/BoardConfigCommon.mk
test -f hardware/samsung_slsi-linaro/config/BoardConfig7870.mk
test -f kernel/samsung/exynos7870/arch/arm64/configs/exynos7870-j7xelte_defconfig
test -f vendor/samsung/j7xelte/j7xelte-vendor.mk
test -f vendor/samsung/universal7870-common/universal7870-common-vendor.mk
```

Review patches/README.md and patches/apply_patches.sh before applying anything. The upstream patch list identifies frameworks/base and lineage-sdk fixes as active. Some other patches are already merged, unnecessary on its base, or carry policy/UI workarounds; do not blindly apply them or suppress security/compatibility failures. Use git apply --check for each selected patch in the correct project, then record the exact applied patch commits.

## Initial compile

```bash
source build/envsetup.sh
lunch lineage_j7xelte-userdebug
set -o pipefail
m bootimage recoveryimage -j4 2>&1 | tee j7xelte-first-build.log
```

If those targets succeed, try `m bacon -j4`. The device reference declares lineage_j7xelte-userdebug, the exynos7870-j7xelte_defconfig exists and is arm64 Linux 3.18.140, and BoardConfig7870.mk exists at the expected path. No successful sync or build has been verified yet.

## Migration boundary

The reference tree uses device/samsung/universal7870-common, hardware/samsung_slsi-linaro, 32-bit audio HAL processes in a 64-bit system, and matching Treble-style vendor modules. The old exynos7870-common tree uses samsung_slsi-cm and different init/media/policy assumptions. Do not mix them in a single build by changing only include paths.

After the reference builds, compare the two common trees, port necessary historical customizations into a coherent newer tree and preserve upstream authorship/licensing. Audit device-specific partitions, boot/recovery compatibility, blob ABI, VINTF and enforcing SELinux before attempting a flash. Do not flash reference images before model confirmation, backups and a verified recovery path.
