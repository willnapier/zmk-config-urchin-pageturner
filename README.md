# Urchin right-half: Kindle clicker + Handy remote

Repurposes the **right half only** of an old [Urchin](https://github.com/duckyb/urchin) (34-key, nice!nano) as a standalone BLE remote. Three hosts, no display.

BLE name: **`Urchin-Turn`**.

## Hosts

Combos are adjacent home-row pairs, **from rest only** (`require-prior-idle-ms` 1000, `timeout-ms` 50). Same combo always selects that host — not a toggle.

| Combo | Profile | Machine | Base layer |
|-------|---------|---------|------------|
| **N+E** | 0 | iPhone | arrows on N/O |
| **E+I** | 1 | nimbini | Linux Handy |
| **I+O** | 2 | Mac | Mac Handy |

A Mac is one bond. Kindle-on-Mac is the PAGE layer, not a fourth profile.

**Bootloader** (dead reset switch): from rest, press **N+E+I+O at once**, or **both thumbs**. `NICENANO` should appear over USB. Chord all four together — rolling N then E will hop to the iPhone slot instead.

After a reboot the BT bond is restored and the layer is **Linux Handy** (N = F20 toggle on both computers). Kindle: hold outer thumb, or **N+E** for the iPhone layer. I+O still selects the Mac bond and Cmd+Z undo.

## Keys (Colemak positions on the right home row)

Fit switches on **E** and **I** (between the original N and O). Thumbs stay, **no keycaps**.

### Default (Linux Handy — boot layer)

| Key | HID |
|-----|-----|
| **N** | F20 → `handy --toggle-transcription` (niri + Hammerspoon) |
| **E** | F19 → `handy --cancel` |
| **I** | unbound (was Ctrl+Z, which suspends Helix / SIGKILL in Nushell) |
| **O** | Enter |

Totem `Mod+G` is unchanged. F13–F18 stay screenshot keys.

### iPhone (N+E)

| Key | HID |
|-----|-----|
| **N** | `LEFT` |
| **E** | letter `e` |
| **I** | letter `i` |
| **O** | `RIGHT` |

Thumbs unbound. No hold required to read.

### Thumbs (computers only)

| Thumb | Hold | Layer |
|-------|------|-------|
| **Inner** | `&mo HELIX` | I = `Esc,u` (discard paste in Helix); O = `Esc, Space, p` (DayPage processor) |
| **Outer** | `&mo PAGE` | N/O = arrows (Mac Kindle) |

Release returns to that host’s Handy base. Helix is momentary — no persistent mode.

Toggle and cancel `&trans` through HELIX (F20 / F19).

## Why right-as-standalone works

Stock Urchin sets **left = central**, **right = peripheral**. A peripheral cannot pair with a phone/Mac by itself.

This config builds **`urchin_right` with `CONFIG_ZMK_SPLIT=n`**, so the right controller is a normal BLE keyboard (correct right-half matrix, no left half required).

## Flash

If this board already runs the old two-key Kindle firmware, **`settings_reset.uf2` first** so the new 3-slot profile table is clean, then the page-turner UF2, then pair:

1. Double-tap reset on the **right** nice!nano → `NICENANO` drive.
2. Flash **`settings_reset.uf2`** once.
3. Double-tap reset again.
4. Flash **`urchin_right_pageturner.uf2`**.
5. Combo **N+E**, pair **Urchin-Turn** on the iPhone.
6. Combo **E+I**, pair on nimbini.
7. Combo **I+O**, pair on the Mac.

## Build

GitHub Actions (push to `main`, or *Run workflow*) uploads `firmware` artifacts.

Local (with west + zmk toolchain):

```bash
west init -l config
west update
west zephyr-export
west build -s zmk/app -b nice_nano_v2 -- -DSHIELD=urchin_right -DZMK_CONFIG="$PWD/config"
```

Host binds and the Handy loop: `~/Assistants/shared/LOCAL-DICTATION.md`.
