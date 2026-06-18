# zmk-config

ZMK firmware configuration for [Cornix LP](https://github.com/hitsmaxft/zmk-keyboard-cornix) split keyboard with rotary encoders.

[日本語版はこちら](README.ja.md)

## Design

This keymap pairs **urob-style "timeless" homerow mods (HRM)** with a small set of combos. The home row carries the modifiers (GUI / Alt / Shift / Ctrl), while editing keys (Esc, Tab, Enter, Backspace, etc.) and a leader key live on combos. A `require-prior-idle-ms` gate keeps the mods out of fast typing, so rolls and rapid sequences never trip a modifier. The keyboard also ships with **ZMK Studio** support for live editing over USB.

> An earlier revision removed homerow mods entirely in favor of modifier combos. That was reverted in 2026-06: the real culprit was a Shift hold-tap with `require-prior-idle-ms = 0`, not HRM itself. Unifying every mod at `150 ms` fixed the misfires, and HRM is back.

See [Combo Reference](docs/combos.md), [Configuration Reference](docs/configuration.md), and [Keymap Design Log](docs/keymap-design.ja.md) (Japanese, full design history).

## Keymap

![Cornix LP Keymap](docs/keymap.svg)

## Quick Reference (for trying the keyboard)

### Home Row Mods

Hold a home-row key to get its modifier; tap (or roll quickly) to get the letter. Mods only fire on a deliberate, isolated hold thanks to the `require-prior-idle-ms = 150` gate.

| Left | Mod | | Right | Mod |
|------|-----|-|-------|-----|
| A | ⌘ GUI | | J | ⌃ Ctrl |
| S | ⌥ Alt | | K | ⇧ Shift |
| D | ⇧ Shift | | L | ⌥ Alt |
| F | ⌃ Ctrl | | ; | ⌘ GUI |

Stack modifiers by holding several at once — **same-hand chords work too** (e.g. F + D held = Ctrl+Shift). `hold-trigger-on-release` defers the positional check to release, so keys you *hold* become mods while a quick same-hand *roll* still types letters. `G` hold = Navi layer; `H` is a plain key.

### Combos (press both keys together)

All combos require a brief idle before they fire (`require-prior-idle-ms = 175`) so rolls during fast typing never trip them. The press-together window (`timeout-ms`) is tuned by finger geometry in three tiers: same-finger vertical pairs (the symbol columns) use a tight `30`, different-finger horizontal pairs (Delete, Backspace, Insert, the brackets/quotes) use `60`, and the wide index+pinky Enter (J + ;) uses `75`. See the [Combo Reference](docs/combos.md) for the full list and tuning notes.

**Editing / navigation**

| Combo | Keys | Action |
|-------|------|--------|
| Esc | W + E | Escape |
| Tab | S + D | Tab |
| Delete | U + I | Delete |
| Backspace | I + O | Backspace (Shift + this = Delete) |
| Insert | O + J | Insert |
| Enter | J + ; | Enter (slightly wider timeout) |
| Leader | D + F | Enter leader mode — but **Alt + D + F = Alt+Tab** (see below) |

**Symbols** (no Shift needed)

| `@` | `#` | `$` | `%` | `^` | `+` | `*` | `&` |
|-----|-----|-----|-----|-----|-----|-----|-----|
| W+S | E+D | R+F | T+G | Y+H | U+J | I+K | O+L |

| `` ` `` | `\` | `=` | `~` | `_` | `-` | `/` | `\|` |
|---------|-----|-----|-----|-----|-----|-----|------|
| S+X | D+C | F+V | G+B | H+N | J+M | K+, | L+. |

**Brackets / quotes** (Shift gives the angle/brace variant)

| Combo | Keys | Tap | Shift |
|-------|------|-----|-------|
| `'` | J + L | `'` | `"` |
| `(` | J + K | `(` | `<` |
| `)` | K + L | `)` | `>` |
| `[` | M + , | `[` | `{` |
| `]` | , + . | `]` | `}` |

**Insert / Delete derivatives**

| Combo | Keys | Action |
|-------|------|--------|
| Ctrl+Insert | X + C | Copy (terminal-style) |
| Shift+Insert | C + V | Paste (terminal-style) |
| Shift+Delete | X + V | Cut (terminal-style) |

### Leader sequences

Tap the **Leader** combo (D + F), then type a sequence. (If Alt is held while pressing D + F, it sends Alt+Tab instead of entering leader mode — keep Alt held and tap D + F repeatedly to cycle windows.)

| Sequence | Action |
|----------|--------|
| `c` `c` | Ctrl twice (e.g. IDE "Run Anything") |
| `s` `s` | Shift twice (e.g. IDE "Search Everywhere") |

## Build

Firmware is built automatically via GitHub Actions on push.
Download `.uf2` files from the [latest Actions run](https://github.com/LevNas/zmk-config/actions).

### Flashing

1. Put the keyboard half into UF2 bootloader mode (double-tap reset button)
2. Copy `cornix_left.uf2` to the left half, `cornix_right.uf2` to the right half
3. If switching from another firmware, flash `reset.uf2` to both halves first to clear BT bonding

### ZMK Studio (Live Editing)

After flashing, connect the **left half** to your PC via USB and open <https://zmk.studio/> in Chrome or Edge to live-edit the keymap. Note: Studio cannot edit behavior definitions (mod-morphs, layer-taps, encoder mappings) — those require source edits and rebuild.

## Customization

Want to create your own keymap? Click **"Use this template"** on GitHub to get started. See the [Setup Guide](docs/fork-guide.md) for details.

## Hardware

- Board: Cornix LP (split, nRF52840)
- Rotary encoders: 2 (left: volume / right: page up-down on base layer; per-layer overrides)
- No dongle

## Credits

- [hitsmaxft](https://github.com/hitsmaxft) — [Cornix LP](https://github.com/hitsmaxft/zmk-keyboard-cornix) board design and ZMK board definition
- [urob](https://github.com/urob) — [zmk-helpers](https://github.com/urob/zmk-helpers) macros, [timeless homerow mods](https://github.com/urob/zmk-config#timeless-homerow-mods) design, and the [zmk-leader-key](https://github.com/urob/zmk-leader-key) module
- [ZMK Contributors](https://zmk.dev/) — ZMK firmware and ZMK Studio
