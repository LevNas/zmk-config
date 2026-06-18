# Combo Reference

All combos defined in `config/cornix.keymap`.

Every combo shares one idle gate, `require-prior-idle-ms = 175` (`COMBO_IDLE`), which suppresses combos in the middle of a typing stream. The press-together window, `timeout-ms`, is set by finger geometry in **three tiers**: same-finger vertical pairs use `30` (`COMBO_TERM`), different-finger horizontal pairs use `60` (`COMBO_TERM_HORIZ`), and the wide index+pinky Enter (J + ;) uses `75` (`COMBO_TERM_WIDE`). See [Combo Misfire Tuning](#combo-misfire-tuning) for the rationale.

## Design Philosophy

This keymap combines **urob-style "timeless" homerow mods (HRM)** with a large set of combos:

- **Modifiers** (Shift, Ctrl, Alt, GUI) live on the home row as homerow mods — see [Home Row Mods](#home-row-mods) below.
- **Editing keys** (Esc, Tab, Delete, Backspace, Insert, Enter), the **leader key**, and the bulk of the **symbols / brackets** live on combos.

The `require-prior-idle-ms` gate that keeps HRM out of fast typing also gates every combo (at 175 ms), so neither mods nor combos fire during rolls or rapid sequences.

> The previous revision routed all modifiers through dedicated combos (`cb_lshft`, `cb_rctrl`, etc.) with no homerow mods. Those eight modifier combos were removed in 2026-06 when timeless HRM was reintroduced. The combo set was then greatly expanded (symbols, brackets, Ins/Del derivatives) and re-tuned for misfire resistance. See the [Keymap Design Log](keymap-design.ja.md) for the full history.

## Home Row Mods

Modifiers are accessed by holding a home-row key (tap = the letter, hold = the modifier). They are **not combos** — see [Configuration Reference](configuration.md) for the `hm_l` / `hm_r` behavior definitions.

| Left | Mod | | Right | Mod |
|------|-----|-|-------|-----|
| A | GUI | | J | Ctrl |
| S | Alt | | K | Shift |
| D | Shift | | L | Alt |
| F | Ctrl | | ; | GUI |

`G` is a layer-tap (`lt_g 4`, hold = Navi); `H` is a plain key.

The `require-prior-idle-ms = 150` gate is the heart of the "timeless" design: any key pressed within 150 ms of the previous keypress resolves to a tap even if held, so fast rolls and repeats never emit a modifier — zero added latency. A modifier only fires on a deliberate, isolated hold after a brief pause.

### Multi-Modifier Combinations

Hold several home-row keys at once to stack modifiers:

- **Same-hand combos** (e.g. Ctrl+Shift): hold F + D on the left, or J + K on the right.
- **Cross-hand combos** (e.g. Ctrl+Alt): hold one key from each hand (e.g. F left + L right).

Because the mods are positional (`hold-trigger-key-positions` restricts triggering to the opposite hand + thumbs), holding a home-row key while typing a same-hand letter still produces the letter, not the modifier.

## Editing / Navigation Combos

| Combo  | Keys (position) | Action | Notes |
|--------|----------------|--------|-------|
| Esc    | W + E (1+2) | `&kp ESC` | Esc is **combo-only**; the thumb that previously tapped Esc is now `&mo 5` (NumPad hold unchanged) |
| Tab    | S + D (13+14) | `&kp TAB` | |
| Delete | U + I (8+9) | `&kp DEL` | Dedicated Delete (new) |
| Backspace | I + O (9+10) | `&bs_del` | Shift + this combo → Delete (mod-morph) |
| Insert | O + J (10+20) | `&kp INS` | Cross-hand |
| Enter  | J + ; (20+23) | `&kp ENTER` | Widest span; loosest `timeout-ms = 75` (`COMBO_TERM_WIDE`); also on right thumb (position 44) |
| Leader | D + F (14+15) | `&df_lead` | Normally leader mode; **Alt held → Tab** (Alt+Tab). See [Leader Key](#leader-key). |

### Delete: dedicated combo *and* mod-morph

There are now **two** ways to send Delete:

1. **Dedicated combo U + I** → `&kp DEL` directly.
2. **Shift + Backspace combo** → the `bs_del` mod-morph emits Delete.

| Operation | Result |
|-----------|--------|
| U + I | Delete |
| I + O alone | Backspace |
| hold D (Shift) + I + O | Delete (Shift + Backspace morphs to DEL) |
| hold K (Shift) + I + O | Delete |

The mod-morph path uses ZMK's `bs_del` behavior defined in `config/cornix.keymap`.

> The thumb Esc (`&lt 5 ESCAPE`) was replaced by a plain `&mo 5`: tapping the thumb no longer types Esc — Esc now lives **only** on the W + E combo. Holding the same thumb still activates the NumPad (layer 5) as before.

## Symbol Combos

Each symbol is a **vertical pair** on the base layer (two stacked keys in the same column), bound to a plain `&kp`. No Shift or layer change is needed.

| Symbol | Keys (position) | Symbol | Keys (position) |
|--------|-----------------|--------|-----------------|
| `@`    | W + S (1+13)  | `` ` `` | S + X (13+25) |
| `#`    | E + D (2+14)  | `\`     | D + C (14+26) |
| `$`    | R + F (3+15)  | `=`     | F + V (15+27) |
| `%`    | T + G (4+16)  | `~`     | G + B (16+28) |
| `^`    | Y + H (7+19)  | `_`     | H + N (19+33) |
| `+`    | U + J (8+20)  | `-`     | J + M (20+34) |
| `*`    | I + K (9+21)  | `/`     | K + , (21+35) |
| `&`    | O + L (10+22) | `\|`    | L + . (22+36) |

> `~` (G + B) replaced the old Space combo. Space stays on the left thumb (`lt 4 SPACE`).

## Bracket & Quote Combos (Shift-Morphed)

These are combos that emit a bracket/quote normally, and the angle/brace variant when Shift is held — same `mod-morph` pattern as `bs_del`. The behaviors are `cm_quote`, `cm_lpar`, `cm_rpar`, `cm_lbkt`, `cm_rbkt` (see [Configuration Reference](configuration.md)).

| Combo | Keys (position) | Tap | Shift + combo |
|-------|-----------------|-----|---------------|
| `'`   | J + L (20+22) | `'` | `"` |
| `(`   | J + K (20+21) | `(` | `<` |
| `)`   | K + L (21+22) | `)` | `>` |
| `[`   | M + , (34+35) | `[` | `{` |
| `]`   | , + . (35+36) | `]` | `}` |

> The old `k;` (K + ;) apostrophe combo was removed; `'` now lives on J + L.

## Insert / Delete Derivative Combos

Terminal-style copy/paste/cut chords.

| Combo | Keys (position) | Action |
|-------|-----------------|--------|
| Ctrl+Insert  | X + C (25+26) | `&kp LC(INS)` |
| Shift+Insert | C + V (26+27) | `&kp LS(INS)` |
| Shift+Delete | X + V (25+27) | `&kp LS(DEL)` |

## Combo Misfire Tuning

The expanded combo set sits directly on the letters used for typing, so misfire resistance is tuned deliberately. The two controls work on **separate axes**: a **false negative** (the combo fails to fire) is fixed by **raising `timeout-ms`**, while a **false positive** (a roll trips the combo) is suppressed by **`require-prior-idle-ms`**.

- **`require-prior-idle-ms = 175` (COMBO_IDLE)** on every combo disables it in the middle of a typing stream — a combo only fires after a brief pause. This is what prevents bigram rolls (e.g. "io", "ui") from misfiring, so the `timeout-ms` window can be loosened without reintroducing in-stream false positives.
- **`timeout-ms` in three geometry tiers**:
  - **`30` (COMBO_TERM)** — same-finger vertical pairs. One finger hits both stacked keys, so there is no inter-finger stagger and a tight window registers cleanly. These pairs are also structurally roll-safe (see below), so a short timeout costs no false-positive protection.
  - **`60` (COMBO_TERM_HORIZ)** — different-finger horizontal pairs (Delete U+I, Backspace I+O, Insert O+J, `'` J+L, `(` J+K, `)` K+L, `[` M+,, `]` ,+.). Two different fingers press adjacent keys with a stagger (longer middle finger leads, pinky lags), so `30` left the two keys un-aligned and the combo failed to fire (FN); these were loosened to `60`.
  - **`75` (COMBO_TERM_WIDE)** — the Enter chord (J + ;), the widest span (index + pinky); the pinky lags the most, so the loosest window.

Two structural notes:

- **Same-finger vertical combos** (the symbol column pairs: ws / ed / rf / tg / yh / uj / ik / ol and sx / dc / fv / gb / hn / jm / k, / l.) are inherently roll-safe — one finger cannot roll across two keys in its own column — which is why they stay at the tight `30`. The same finger also cannot continuously re-fire a vertical pair during normal typing, so they are structurally strong against false positives.
- The right-hand home cluster **J / K / L** carries the densest combo overlap (`'`, `(`, `)`, plus the `+ * &` symbol column and the J + ; Enter), making it the highest misfire-risk zone. Watch this area first when tuning.

These three tiers are the **current provisional values from on-device tuning** and may be adjusted as long-term use surfaces false positives.

## Leader Key

A **leader key** (the urob [zmk-leader-key](https://github.com/urob/zmk-leader-key) module, provided as a west module since it is not yet in upstream ZMK `main`) opens a small command namespace. Tap the **Leader** combo (D + F) to enter leader mode, then type a key sequence to fire its binding.

The Leader combo is a `mod-morph` (`df_lead`): with no modifier it runs `&leader`, but **while Alt is held it sends Tab** (`keep-mods` preserves Alt, so it becomes Alt+Tab). Hold Alt and tap D + F repeatedly to cycle through windows.

This module variant is **modal**: it has no timeout, no layer scoping, and no immediate-trigger. It waits until a valid sequence completes, and any key not part of a valid sequence aborts it.

| Sequence | Action | Example use |
|----------|--------|-------------|
| `c` `c` | `&ctrl_ctrl` — taps Ctrl twice | IDE "Run Anything" |
| `s` `s` | `&shift_shift` — taps Shift twice | IDE "Search Everywhere" |

The double-tap macros use `wait-ms = tap-ms = 40`. The interval the host accepts as a "double tap" is IDE-dependent, so adjust those values if the gesture is not recognized.

**When to use a combo vs. the leader**: a frequently used single action is faster on a direct combo (one chord) than through the leader (at least two keypresses). The leader is best for low-frequency, numerous, mnemonic commands grouped into a namespace.

## Brackets on the Symbols Layer (alternative path)

In addition to the bracket/quote **combos** above, the same brackets are also available as direct key bindings on the **Symbols layer** (top row and home row). The combos are the primary path; the Symbols layer remains as a fallback / for sustained bracket entry.

| Symbol | Symbols Layer Position | Source key |
|--------|------------------------|------------|
| `<`    | top row pos 0 | Q |
| `>`    | top row pos 1 | W |
| `[`    | top row pos 2 | E |
| `]`    | top row pos 3 | R |
| `{`    | top row pos 4 | T |
| `}`    | right top row pos 7 | (next to `` ` ``) |
| `(`    | home row | L (also Shift+9 = `(`) |
| `)`    | home row | ; (also Shift+0 = `)`) |

To use: hold thumb `mo 1` (position 42) to activate Symbols, then press the corresponding key.

## Editing Notes

- **ZMK Studio** can view layouts and edit static keymaps, but **cannot edit behaviors** (`bs_del`, `hm_l`/`hm_r`, layer-tap, leader, etc.). Modifications to behaviors require editing `config/cornix.keymap` and rebuilding.
- Keymap Editor is **not recommended** — past versions silently rewrote combo positions on save, leading to inconsistent state.
