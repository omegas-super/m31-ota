# Evolution X 12.4 for Samsung Galaxy M31 (SM-M315F)

OTA hosting and build information for unofficial Evolution X 12.4 (Android 17) builds for the
Samsung Galaxy M31 / M31s (`m31`, Exynos 9611).

## Latest build — 2026-11-22

- **File:** `EvolutionX-17.0-20261122-m31-12.4-Vanilla-Unofficial.zip`
- **Download:** [GitHub release v12.4-20261122-upstream](https://github.com/omegas-super/m31-ota/releases/tag/v12.4-20261122-upstream)
- **Size:** 1,734,951,680 bytes
- **SHA-256:** `67c99e0f353a00ef8101c6712246bbff30411175675ffb94a7ab4c499f49693a`
- **Build timestamp:** 1795276122
- **Android:** 17 (SDK 37), security patch 2026-11-01
- **Build type:** Vanilla, userdebug
- **Build result:** 9,627 build steps, `Build Succeeded`, `unzip -t` clean

## Changelog — 2026-11-22 vs 2026-11-16

### Source and architecture

- Synced all repos to upstream with `repo sync -c -j8 --no-tags --no-clone-bundle --fail-fast`
  (rc=0). Local changes were backed up first to
  `/home/Alma/m31-upstream-backup-20261121T122600Z`.
- Device trees are at Parbindar7 `lineage-24.0` upstream commits: m31 `da6d18f`,
  universal9611-common `25a32eb`, kernel `c19ad6b24`.
- Verified 64-bit configuration against Parbindar7 sources with six research agents and
  adversarial critics. No change was needed: `TARGET_ARCH := arm64`,
  `TARGET_2ND_ARCH := arm` for 32-bit vendor HALs, inherited `core_64_bit_only.mk`
  (zygote64, 64-bit apps on, 32-bit apps off), `CONFIG_64BIT=y`, `CONFIG_ARM64=y`,
  `CONFIG_COMPAT=y`. Adding explicit `TARGET_64BIT`-style flags would be a no-op or harmful.

### Changes applied to the ROM

1. **Evolution X Updater** — baked in
   `evolution.updater.uri=https://raw.githubusercontent.com/omegas-super/m31-ota/main/{device}.json`
   in `vendor/build.prop`, so the Updater app finds OTA builds from this repo.
2. **NikGapps survival** — `/system/addon.d/71-gapps-survival.sh` is inside the system
   image and preserves Google apps across dirty flashes.
3. **Launcher dexopt** — ported the device-independent part of PenguinOS ART commit
   `d1193a0f`: `dalvik.vm.launchercompilerfilter=speed` and
   `dalvik.vm.systemuicompilerfilter=speed`, with `PRODUCT_DEXPREOPT_SPEED_APPS +=
   Launcher3QuickStep SystemUI`. The upstream patch's malformed exception string was
   corrected.
4. **Lighter blur path** — `debug.renderengine.blur_algorithm=kawase_darkmoon`,
   Evolution X's low-end blur backend, for the Exynos 9611 Mali-G72. This reduces GPU
   work from blurs; predicted HWC composition and frame-rate overrides remain at their
   proven device values.
5. **Build fix after upstream sync** — restored the Exynos gralloc4 Soong namespace
   import in `hardware/samsung_slsi-linaro/graphics/Android.bp` and added the matching
   product namespaces, fixing `undefined module "libgralloc4_headers_exynos"`.
6. **Kernel config** — `CONFIG_SELINUX_STATUS="dynamic"` retained for runtime SELinux
   toggling on userdebug.
7. **Lunch choices** — `lineage_m31-cp2a-eng/user/userdebug` added in `AndroidProducts.mk`.

### What was deliberately rejected

- No "parallel rendering" prop: research found no such PenguinOS feature; those paths
  are stock AOSP.
- No GameSpace/QTI HAL patches, per-FPS duration or LTPO changes: Qualcomm/GKI-specific.
- No forced Liquid Glass: Evolution X already ships the same shader under renamed
  properties, and forcing it on costs frames on the Mali-G72.
- No duplicate LMKD prop pins: they were already at AOSP defaults.

## OTA

The updater reads [m31.json](m31.json) from this repo. It currently points to
`v12.4-20261122-upstream` with the SHA-256 and size above. After installing this build,
the Evolution X Updater app will offer future releases automatically.

OTA downloads use GitHub release assets. The JSON follows the Evolution X updater schema
(`buildtype`, `download`, `filename`, `maintainer`, `os_patch_level`, `os_sdk_level`,
`sha256`, `size`, `timestamp`, `version`).

## Flash instructions

1. Boot TWRP/OrangeFox.
2. From stock or another ROM: back up EFS/modem, then Format Data.
3. Flash `EvolutionX-17.0-20261122-m31-12.4-Vanilla-Unofficial.zip`.
4. Reboot. First boot can take several minutes.
5. NikGapps, if installed, is restored by `/system/addon.d/71-gapps-survival.sh`.

For root, flash a matched SukiSU-Nova / KernelSU-Next boot image from
[m31-sukisu-kernel releases](https://github.com/omegas-super/m31-sukisu-kernel/releases)
with `fastboot flash boot` after the ROM is installed.

## Related repositories and reports

- [OTA manifest](m31.json)
- [Kernel and root releases](https://github.com/omegas-super/m31-sukisu-kernel/releases)
- [HyperOS GSI + PenguinOS research and patches](https://github.com/omegas-super/m31-sukisu-kernel/releases/tag/v4.14.357-ksu-susfs-20261115)

These builds are unofficial and are not boot-tested by the maintainer. Keep a backup
before flashing.
