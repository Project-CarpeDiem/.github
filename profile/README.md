# Project-CarpeDiem

CarpeDiem is a LineageOS-based Android distribution focused on stability,
clean branding, and maintainer-friendly bringup.

- Base: LineageOS `lineage-24.0` / Android 17
- Vendor path: `vendor/lineage` (kept, like other Lineage forks)
- Brand: `CarpeDiem` (`PRODUCT_BRAND ?= CarpeDiem`)
- Version props: `ro.carpediem.version`, `ro.carpediem.display.version`,
  `ro.modversion` (parallel to `ro.lineage.*`, kept for compat)
- Builds: vanilla by default, GApps via flashable zip

## Repositories

All active branches track `lineage-24.0`. Old branches were removed.

| Repo | Purpose |
|------|---------|
| `Project-CarpeDiem/android` | Manifest (`default.xml` + `snippets/carpediem.xml`) |
| `Project-CarpeDiem/android_build` | `build/make` |
| `Project-CarpeDiem/android_build_soong` | `build/soong` |
| `Project-CarpeDiem/android_frameworks_base` | Framework features |
| `Project-CarpeDiem/android_packages_apps_Settings` | Settings / About phone |
| `Project-CarpeDiem/android_packages_apps_Updater` | OTA updater |
| `Project-CarpeDiem/android_packages_apps_LineageParts` | Device features (to be renamed CarpeDiemParts) |
| `Project-CarpeDiem/android_vendor_lineage` | Branding: `config/version.mk`, `config/common.mk`, bootanimation |
| `Project-CarpeDiem/android_lineage-sdk` | Lineage SDK |
| `Project-CarpeDiem/android_hardware_lineage_compat` | Compat HALs |
| `Project-CarpeDiem/android_hardware_lineage_interfaces` | Lineage interfaces |

Device, kernel, and proprietary vendor trees are maintainer-owned and
referenced from the manifest per device. They are not centralized here.

## Building

```bash
repo init -u git@github.com:Project-CarpeDiem/android.git -b lineage-24.0 --git-lfs
repo sync -c --no-tags --no-clone-bundle -j$(nproc)

source build/envsetup.sh
lunch lineage_<device>-userdebug
mka bacon -j$(nproc)
```

Out: `out/target/product/<device>/`.

### GApps

CarpeDiem ships vanilla. Flash MindTheGapps / NikGapps for Android 17
after the ROM. Built-in GApps builds are a separate CI flavor:

```bash
export WITH_GMS=true
# optional: export GMS_MAKEFILE=gms_minimal.mk
lunch lineage_<device>-userdebug
mka bacon
```

Requires `vendor/partner_gms` present and `release-keys` signing.

## Versioning

`vendor/lineage/config/version.mk`:

- `LINEAGE_VERSION`, `LINEAGE_BUILDTYPE` untouched (build system compat)
- `CARPEDIEM_VERSION := CarpeDiem-24.0-<date>-<type>-<build>`
- `getprop ro.carpediem.version ro.modversion`

## Contributing

1. Fork from `lineage-24.0`, keep branch name `lineage-24.0`.
2. Rebase on upstream LineageOS, don't merge.
3. One feature per commit. Device bringups must be SELinux enforcing.
4. Manifest changes go through `snippets/carpediem.xml`
   (`<remove-project>` + `<project>`), not `default.xml`.

## Support

- OTA: via Updater (vanilla and GApps channels are separate)
- Bugs: include device, build date (`ro.carpediem.version`), logcat,
  dmesg, and GApps variant if applicable
- Android 17 / `lineage-24.0` is still stabilizing upstream; Pixel
  devices were first, others follow as maintainers validate

## Credits

- LineageOS for the base platform
- AOSP / Google for Android
- MindTheGapps / NikGapps for Google Apps packages
- All device maintainers and contributors
