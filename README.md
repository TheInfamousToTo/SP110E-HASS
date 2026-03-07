# SP110E Home Assistant Integration

Control SP110E RGB LED BLE Controller from Home Assistant.

> **This is a modernized fork of [roslovets/SP110E-HASS](https://github.com/roslovets/SP110E-HASS)** updated to work with Home Assistant 2022.0+ and modern versions of the `bleak` BLE library. The original repo is no longer maintained and breaks on all current HA versions.

[![hacs_badge](https://img.shields.io/badge/HACS-Custom-orange.svg)](https://github.com/hacs/integration)

---

## What's Fixed vs Original

| Issue | Fix |
|-------|-----|
| `ImportError: cannot import name 'COLOR_MODE_RGBW'` | Replaced with `ColorMode.RGBW` (HA 2022+ API) |
| `ImportError: cannot import name 'SUPPORT_EFFECT'` | Replaced with `LightEntityFeature.EFFECT` |
| `ImportError: cannot import name 'discover' from 'bleak'` | Updated to `BleakScanner.discover()` |
| BLE state never updates (light always shows as off) | Fixed `_callback_handler` sender check — newer bleak passes a `BleakGATTCharacteristic` object, not integer `12` |
| `TypeError: unsupported operand type(s) for \|: 'list' and 'set'` | `supported_color_modes` now returns a `set` as required by modern HA |
| pip dependency fails silently on HAOS | `sp110e` library is now bundled directly inside the component — no pip install needed |
| All internal imports broken after bundling | Fixed all imports to use relative paths |

**Tested on:** Home Assistant 2026.3.0, Raspberry Pi 4 (built-in Bluetooth), Python 3.14

---

## Requirements

- Home Assistant 2022.0 or newer
- SP110E BLE LED controller
- Bluetooth adapter accessible to Home Assistant

---

## Installation

### Via HACS (Recommended)

1. Open HACS in Home Assistant
2. Go to **Integrations**
3. Click the three dots menu → **Custom repositories**
4. Add `https://github.com/TheInfamousToTo/SP110E-HASS` as an **Integration**
5. Find **SP110E RGB LED BLE Controller** and install it
6. Restart Home Assistant

### Manual

1. Copy the `custom_components/sp110e` folder into your HA config directory:
   ```
   /config/custom_components/sp110e/
   ```
2. Restart Home Assistant

---

## Configuration

Add to your `configuration.yaml`:

```yaml
light:
  - platform: sp110e
    mac: 68:86:1A:04:82:A7   # Your SP110E MAC address
    name: LED Strip           # Any name you like
    ic_model: WS2811          # Check your LED strip IC (WS2811, WS2812, UCS1903, etc.)
    sequence: GRB             # Color sequence (RGB, GRB, BGR, RBG, GBR, BRG)
    pixels: 150               # Number of LEDs on your strip
    strict: false
```

### Finding Your MAC Address
Open the **LED Hue** app (official SP110E app) — it shows the MAC address and IC model when connecting to your device.

### Configuration Options

| Option | Required | Default | Description |
|--------|----------|---------|-------------|
| `mac` | Yes | — | BLE MAC address of your SP110E |
| `name` | No | `SP110E` | Friendly name for the entity |
| `ic_model` | No | `''` | IC model of your LED strip (e.g. `WS2811`, `WS2812`, `UCS1903`) |
| `sequence` | No | `''` | Color byte sequence (e.g. `GRB`, `RGB`, `BGR`) |
| `pixels` | No | `0` | Number of pixels/LEDs |
| `speed` | No | `256` | Speed of automatic effect modes (0–255). Default `256` means "use device default" (speed is not written to the device) |
| `strict` | No | `false` | Raise exceptions on BLE errors instead of silently ignoring |
| `add_effects` | No | `[]` | List of custom named presets (see [Custom Effects](#custom-effects) below) |

---

## Custom Effects

You can define named presets that appear in the Home Assistant effects list. Each preset can fix a specific mode, speed, brightness, colour, and/or white level:

```yaml
light:
  - platform: sp110e
    mac: 68:86:1A:04:82:A7
    name: LED Strip
    add_effects:
      - name: "Warm White"
        state: true
        white: 200
        brightness: 180
      - name: "Party Rainbow"
        state: true
        mode: 3
        speed: 100
        color: [255, 0, 128]
      - name: "Off"
        state: false
```

### Custom Effect Fields

| Field | Required | Default | Description |
|-------|----------|---------|-------------|
| `name` | Yes | — | Name shown in the HA effects list |
| `state` | No | `true` | Whether the light is on for this preset |
| `mode` | No | `null` | Auto-cycle mode number (0–121). `0` = demo |
| `speed` | No | `null` | Speed for this preset (0–255) |
| `brightness` | No | `null` | Brightness for this preset (0–255) |
| `color` | No | `null` | RGB colour as `[R, G, B]` (0–255 each) |
| `white` | No | `null` | White LED level for this preset (0–255) |

Built-in mode numbers (0–121) are always appended to the effects list automatically.

---

## Supported IC Models

`SM16703`, `TM1804`, `UCS1903`, `WS2811`, `WS2801`, `SK6812`, `LPD6803`, `LPD8806`, `APA102`, `APA105`, `DMX512`, `TM1914`, `TM1913`, `P9813`, `INK1003`, `P943S`, `P9411`, `P9413`, `TX1812`, `TX1813`, `GS8206`, `GS8208`, `SK9822`, `TM1814`, `SK6812_RGBW`, `P9414`, `PG412`

---

## Useful Automations

**Toggle:**
```yaml
action: light.toggle
target:
  entity_id: light.led_strip
```

**Increase brightness by 10%:**
```yaml
action: light.turn_on
target:
  entity_id: light.led_strip
data:
  brightness_step_pct: 10
```

**Decrease brightness by 10%:**
```yaml
action: light.turn_on
target:
  entity_id: light.led_strip
data:
  brightness_step_pct: -10
```

---

## Troubleshooting

**Light shows as unavailable**
- Make sure the SP110E is powered on
- Close the LED Hue app on your phone — the SP110E only supports one BLE connection at a time
- Check your MAC address is correct

**Colors appear wrong**
- Try different `sequence` values: `RGB`, `GRB`, `BGR`, `RBG`, `GBR`, `BRG`

**State shows as off even when light is on**
- This was a bug in the original integration — it's fixed in this fork

---

## Support This Project

This fork exists because the original integration was broken on every current version of Home Assistant. Fixing it, bundling the library, testing on real hardware, and keeping it up to date took significant effort.

If this integration saves you time and frustration, consider buying me a coffee ☕ — it goes a long way toward keeping the project maintained.

[![Buy Me a Coffee](https://img.shields.io/badge/Buy%20Me%20a%20Coffee-donate-yellow?logo=buy-me-a-coffee&logoColor=white)](https://www.buymeacoffee.com/TheInfamousToTo)
[![GitHub Sponsors](https://img.shields.io/badge/GitHub%20Sponsors-sponsor-ea4aaa?logo=github-sponsors&logoColor=white)](https://github.com/sponsors/TheInfamousToTo)

You can also help by:
- ⭐ **Starring the repo** — it helps others find the integration
- 🐛 **Reporting bugs** via [GitHub Issues](https://github.com/TheInfamousToTo/SP110E-HASS/issues)
- 📣 **Sharing** with other SP110E users in the Home Assistant community

---

## Credits

- Original integration: [Pavel Roslovets](https://github.com/roslovets/SP110E-HASS)
- Modernization & compatibility fixes: [@TheInfamousToTo](https://github.com/TheInfamousToTo)
