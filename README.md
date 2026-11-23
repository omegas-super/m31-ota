# Evolution X 12.4 for Samsung Galaxy M31 (SM-M315F)

OTA hosting and build information for unofficial Evolution X 12.4 (Android 17) builds for the
Samsung Galaxy M31 / M31s (`m31`, Exynos 9611).

## Latest stable build — 2026-11-23

- **File:** `EvolutionX-17.0-20261123-m31-12.4-Vanilla-Unofficial.zip`
- **Download:** [GitHub release v12.4-20261123-stable](https://github.com/omegas-super/m31-ota/releases/tag/v12.4-20261123-stable)
- **Size:** 1,734,951,332 bytes
- **SHA-256:** `373294174b753d47da6caa987a1a11b6e88185b2383e7dc4a31f9720d618691d`
- **Build timestamp:** 1795378289
- **Android:** 17 (SDK 37), security patch 2026-11-01
- **Build type:** Vanilla, userdebug
- **Build result:** `Build Succeeded`, `unzip -t` clean

This is the replacement for the 2026-11-22 experimental build, which bootlooped on device.
Use this stable build instead.

## Changelog — stable 2026-11-23 vs experimental 2026-11-22

### Reverted experimental changes

The following were removed because they were the likely cause of the bootloop:

- PenguinOS ART launcher-compiler-filter patch in `Dexopter.java`.
- `dalvik.vm.launchercompilerfilter=speed`.
- `dalvik.vm.systemuicompilerfilter=speed`.
- `PRODUCT_DEXPREOPT_SPEED_APPS += Launcher3QuickStep SystemUI`.
- `debug.renderengine.blur_algorithm=kawase_darkmoon`.

Verified in the final build output: no ART patch, no compiler-filter props, no blur override.

### Kept stable features

- **Evolution X Updater** — `evolution.updater.uri=https://raw.githubusercontent.com/omegas-super/m31-ota/main/{device}.json`
  in `vendor/build.prop`.
- **NikGapps survival** — `/system/addon.d/71-gapps-survival.sh` inside the system image.
- **Device configuration** — `EVO_BUILD_TYPE := Unofficial`, fingerprint override disabled,
  boot animations disabled, quick tap disabled, and `lineage_m31-cp2a-eng/user/userdebug`
  lunch choices.
- **Kernel config** — `CONFIG_SELINUX_STATUS="dynamic"`.
- **Build fix** — Exynos gralloc4 Soong namespace import and product namespace entry,
  required because upstream split gralloc4 into its own Soong namespace after the previous
  working build.

### Source and architecture

- All repositories were synced to upstream with `repo sync -c -j8 --no-tags --no-clone-bundle
  --fail-fast`.
- Device/kernel trees are at Parbindar7 `lineage-24.0` heads: m31 `da6d18f`,
  universal9611-common `25a32eb`, kernel `c19ad6b24`.
- The 64-bit configuration was audited against Parbindar7 sources and found correct:
  `TARGET_ARCH := arm64`, `TARGET_2ND_ARCH := arm` for 32-bit vendor HALs, inherited
  `core_64_bit_only.mk` (zygote64, 64-bit apps on, 32-bit apps off), `CONFIG_64BIT=y`,
  `CONFIG_ARM64=y`, `CONFIG_COMPAT=y`.

## OTA

The updater reads [m31.json](m31.json) from this repo. It now points to
`v12.4-20261123-stable` with the SHA-256 and size above. The repository API and
cache-busted raw-URL checks confirm the new manifest; raw.githubusercontent.com CDN caches
may continue serving the previous file for up to five minutes.

OTA downloads use GitHub release assets. The JSON follows the Evolution X updater schema
(`buildtype`, `download`, `filename`, `maintainer`, `os_patch_level`, `os_sdk_level`,
`sha256`, `size`, `timestamp`, `version`).

## Flash instructions

1. Boot TWRP/OrangeFox.
2. From stock or another ROM: back up EFS/modem, then Format Data.
3. Flash `EvolutionX-17.0-20261123-m31-12.4-Vanilla-Unofficial.zip`.
4. Reboot. First boot can take several minutes.
5. NikGapps, if installed, is restored by `/system/addon.d/71-gapps-survival.sh`.

For root, flash a matched SukiSU-Nova / KernelSU-Next boot image from
[m31-sukisu-kernel releases](https://github.com/omegas-super/m31-sukisu-kernel/releases)
with `fastboot flash boot` after the ROM is installed.

## Superseded builds

- `v12.4-20261122-upstream` — experimental PenguinOS-feature build; **do not flash**,
  reported to bootloop.
- Earlier builds remain on GitHub for reference only.

## Related repositories and reports

- [OTA manifest](m31.json)
- [Kernel and root releases](https://github.com/omegas-super/m31-sukisu-kernel/releases)
- [HyperOS GSI + research and patches](https://github.com/omegas-super/m31-sukisu-kernel/releases/tag/v4.14.357-ksu-susfs-20261115)

These builds are unofficial. Keep a backup before flashing.
