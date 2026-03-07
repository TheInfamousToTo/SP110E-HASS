# Changelog

## [2.0.0] - 2026-03-07

### Breaking Changes
- Minimum Home Assistant version is now **2022.0.0**

### Fixed
- **`ImportError: COLOR_MODE_RGBW`** — replaced deprecated constant with `ColorMode.RGBW` (HA 2022+ API change)
- **`ImportError: SUPPORT_EFFECT`** — replaced deprecated constant with `LightEntityFeature.EFFECT` (HA 2022+ API change)
- **`ImportError: discover from bleak`** — updated `bleak.discover()` to `BleakScanner.discover()` (bleak API change)
- **Light always shows as off** — fixed `_callback_handler` in driver: newer bleak passes a `BleakGATTCharacteristic` object as `sender` instead of integer `12`, causing state to never be read back from device
- **`TypeError: unsupported operand type(s) for |: 'list' and 'set'`** — `supported_color_modes` now correctly returns a `set` as required by modern HA
- **pip dependency fails on HAOS** — bundled the `sp110e` Python library directly inside the component, removing the need for a pip install that silently failed on HAOS
- **Broken internal imports** — fixed all internal imports to use relative paths after bundling

### Changed
- Bumped version to `2.0.0` to reflect significant compatibility changes
- Updated `manifest.json`: removed broken pip `requirements`, added `@TheInfamousToTo` as co-maintainer
- Updated documentation URLs to point to this fork
- Added `hacs.json` with minimum HA version

---

## [1.0.2] - Original (roslovets)

Original release by Pavel Roslovets. Works on Home Assistant < 2022.0 only.
