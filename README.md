# Urchin right-half Bluetooth page turner

Repurposes the **right half only** of an old [Urchin](https://github.com/duckyb/urchin) (34-key, nice!nano) as a standalone BLE remote for PDF / ebook page turning.

## Keys

| Physical (Colemak letter) | HID sent   | Notes                          |
|---------------------------|------------|--------------------------------|
| **L** (top row)           | `←` LEFT   | Previous page in most readers  |
| **O** (home-row pinky)    | `→` RIGHT  | Next page                      |
| Inner right thumb         | Soft off   | Deep power-off for storage     |
| Outer right thumb         | BT clear   | Wipe host bonds (re-pair)      |

All other switches are unbound.

BLE name: **`Urchin-Turn`** (avoids clashing with a full-keyboard "urchin" pair).

## Why right-as-standalone works

Stock Urchin sets **left = central**, **right = peripheral**. A peripheral cannot pair with a phone/Mac by itself.

This config builds **`urchin_right` with `CONFIG_ZMK_SPLIT=n`**, so the right controller is a normal BLE keyboard (correct right-half matrix, no left half required).

## Flash (first time)

1. Double-tap reset on the **right** nice!nano → `NICENANO` drive.
2. Flash **`settings_reset.uf2`** once (clears old split/host bonds).
3. Double-tap reset again when the board reboots into bootloader (or reset once more).
4. Flash **`urchin_right_pageturner.uf2`**.
5. On iOS/macOS: Settings → Bluetooth → pair **Urchin-Turn**.
6. Open a PDF/ebook; press **L** / **O**.

## Build

GitHub Actions (push to `main`, or *Run workflow*) uploads `firmware` artifacts.

Local (with west + zmk toolchain):

```bash
west init -l config
west update
west zephyr-export
west build -s zmk/app -b nice_nano_v2 -- -DSHIELD=urchin_right -DZMK_CONFIG="$PWD/config"
```

## Later tweaks

If a reader ignores arrows (some iOS Kindle modes), try swapping to:

- `PG_UP` / `PG_DN`
- `C_VOL_UP` / `C_VOL_DN` (common for iOS page-turn accessories)
- `UP` / `DOWN`

Edit `config/urchin.keymap` and rebuild.
