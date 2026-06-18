# Configuration Reference

Detailed explanation of behaviors, macros, and firmware settings used in this keymap.

## Design Philosophy

This keymap combines **urob-style "timeless" homerow mods (HRM)** with a large set of combos. Modifiers (Shift, Ctrl, Alt, GUI) live on the home row as homerow mods (`hm_l` / `hm_r`); editing keys, symbols, brackets, and a leader key live on combos. A `require-prior-idle-ms = 150` gate keeps the mods out of fast typing; the combos use their own gate (`require-prior-idle-ms = 175`) plus a short `timeout-ms` for misfire resistance.

For combo placement and usage, see [Combo Reference](combos.md). For the design history and decision rationale, see [Keymap Design Log](keymap-design.ja.md) (Japanese only).

## Behaviors

### Homerow Mods: `hm_l` / `hm_r`

Positional hold-tap behaviors used on the home row. Tap sends the letter; hold (after a brief idle) sends the modifier.

```dts
hm_l: homerow_mods_left {
    compatible = "zmk,behavior-hold-tap";
    bindings = <&kp>, <&kp>;
    flavor = "balanced";
    tapping-term-ms = <280>;          // HM_TAPPING_TERM
    quick-tap-ms = <175>;             // HM_QUICK_TAP
    require-prior-idle-ms = <150>;    // HM_PRIOR_IDLE — the "timeless" gate
    hold-trigger-key-positions = <KEYS_R KEYS_T>;  // opposite hand + thumbs
    hold-trigger-on-release;
};
```

`hm_r` is identical but restricts triggering to the left hand (`KEYS_L KEYS_T`). Both share the same timing constants, **including Shift** — the earlier split where Shift used `require-prior-idle-ms = 0` was the root cause of fast-typing misfires and was unified to 150 ms when HRM was reintroduced (2026-06).

Home row assignments (pinky → index):

| Left | Mod | Right | Mod |
|------|-----|-------|-----|
| A | LGUI | J | LCTRL |
| S | LALT | K | LSHFT |
| D | LSHFT | L | LALT |
| F | LCTRL | ; | LGUI |

Why `require-prior-idle-ms = 150` matters: any key pressed within 150 ms of the previous keypress resolves to a tap even when held, so fast rolls and repeats never emit a modifier (zero added latency). A modifier only fires on a deliberate, isolated hold after a pause. `hold-trigger-key-positions` makes the mods positional, so holding a home-row key while typing a same-hand letter still produces the letter.

There is also a non-positional `hm` behavior (no `hold-trigger-key-positions`) kept for occasional use; the home row itself uses the positional `hm_l` / `hm_r`.

### Mod-Morph: `bs_del`

Sends different keycodes depending on whether Shift is held.

```dts
bs_del: bs_del {
    compatible = "zmk,behavior-mod-morph";
    bindings = <&kp BSPC>, <&kp DEL>;
    mods = <(MOD_LSFT|MOD_RSFT)>;
};
```

- **Tap**: Backspace
- **Shift + Tap**: Delete

Used in the `cb_kp_bs` combo (I + O). (A dedicated Delete also exists on the U + I combo via plain `&kp DEL`.)

### Symbol Mod-Morphs: `cm_quote` / `cm_lpar` / `cm_rpar` / `cm_lbkt` / `cm_rbkt`

The bracket/quote combos use the same Shift-driven `mod-morph` pattern as `bs_del`: tap sends one symbol, Shift + tap sends its angle/brace variant.

```dts
cm_quote: cm_quote {
    compatible = "zmk,behavior-mod-morph";
    bindings = <&kp SQT>, <&kp DQT>;   // ' / "
    mods = <(MOD_LSFT|MOD_RSFT)>;
};
```

| Behavior  | Tap | Shift + tap | Combo |
|-----------|-----|-------------|-------|
| `cm_quote` | `'` | `"` | J + L |
| `cm_lpar`  | `(` | `<` | J + K |
| `cm_rpar`  | `)` | `>` | K + L |
| `cm_lbkt`  | `[` | `{` | M + , |
| `cm_rbkt`  | `]` | `}` | , + . |

All five share `mods = <(MOD_LSFT|MOD_RSFT)>`, the same Shift trigger as `bs_del`.

### Leader / Tab Mod-Morph: `df_lead`

The Leader combo (D + F) is itself a `mod-morph`: with no modifier it runs the leader key; while **Alt is held** it sends Tab and keeps Alt down (`keep-mods`), producing Alt+Tab.

```dts
df_lead: df_lead {
    compatible = "zmk,behavior-mod-morph";
    bindings = <&leader>, <&kp TAB>;
    mods = <(MOD_LALT|MOD_RALT)>;
    keep-mods = <(MOD_LALT|MOD_RALT)>;  // keep Alt so Alt+Tab cycles windows
};
```

- **Tap (no Alt)**: enter leader mode (`&leader`).
- **Alt + tap**: Alt+Tab. Hold Alt and tap D + F repeatedly to cycle through windows.

### Layer Tap: `lt`

Hold to activate a layer, tap to send a keycode.

```dts
&lt 4 SPACE     // Tap: Space, Hold: Navi layer
&lt 1 BACKSPACE // Tap: Backspace, Hold: Symbols layer
```

The thumb that holds for NumPad (layer 5) is now a plain `&mo 5` (hold-only), not a layer-tap — tapping it no longer types Esc. Esc moved fully onto the W + E combo.

### Layer Tap (tap-preferred): `lt_g`

A `tap-preferred` variant of layer-tap used for the `G` key (`lt_g 4 G`): tap = `G`, hold = Navi layer. It keeps its own timing constants (`tapping-term-ms = 250`, `require-prior-idle-ms = 70`) rather than inheriting the HRM constants, because that feel was confirmed most natural in testing.

```dts
lt_g: layer_tap_g {
    compatible = "zmk,behavior-hold-tap";
    bindings = <&mo>, <&kp>;
    flavor = "tap-preferred";
    tapping-term-ms = <250>;
    quick-tap-ms = <200>;
    require-prior-idle-ms = <70>;
};
```

### Leader Key: `leader`

A modal leader key from the urob [zmk-leader-key](https://github.com/urob/zmk-leader-key) module (a west module — this feature is not yet in upstream ZMK `main`). Tapping the Leader combo (D + F, via the `df_lead` mod-morph) enters leader mode; a following key sequence fires its binding. This module variant has **no timeout, no layer scoping, and no immediate-trigger**: it waits for a valid sequence and aborts on any invalid key.

```dts
leader: leader {
    compatible = "zmk,behavior-leader-key";
    ldr_cc { sequence = <C C>; bindings = <&ctrl_ctrl>; };    // c c → Ctrl twice
    ldr_ss { sequence = <S S>; bindings = <&shift_shift>; };  // s s → Shift twice
};
```

## Macros

### IDE Gestures: `ctrl_ctrl` / `shift_shift`

Tap the same modifier twice so the host registers a double-tap (used by IDE shortcuts like IntelliJ's "Run Anything" / "Search Everywhere"). Invoked from the leader sequences `c c` and `s s`.

```dts
ctrl_ctrl: ctrl_ctrl {
    compatible = "zmk,behavior-macro";
    wait-ms = <40>;
    tap-ms = <40>;
    bindings = <&kp LCTRL>, <&kp LCTRL>;
};
```

`shift_shift` is identical with `LSHFT`. The interval the host accepts as a "double tap" is IDE-dependent, so adjust `wait-ms` / `tap-ms` if the gesture is not recognized.

## Combo Tuning Constants

Every combo in `config/cornix.keymap` shares a small set of `#define` constants that govern misfire resistance:

The `timeout-ms` window is set by finger geometry in **three tiers**; `require-prior-idle-ms` is uniform (`175`) across every combo.

| Constant | Value | Applied as | Purpose |
|----------|-------|-----------|---------|
| `COMBO_TERM` | `30` | `timeout-ms` (same-finger vertical combos) | One finger hits both stacked keys, so there is no inter-finger lag and a tight window suffices — and same-finger vertical pairs are structurally roll-safe, so a short timeout costs no false-positive protection |
| `COMBO_TERM_HORIZ` | `60` | `timeout-ms` (different-finger horizontal combos) | Adjacent keys pressed by **different** fingers arrive with a stagger (longer middle finger leads, pinky lags), so `30` left the two keys un-aligned and the combo failed to fire (FN); loosened to `60`. Applies to Delete (U + I), Backspace (I + O), Insert (O + J), `'` (J + L), `(` (J + K), `)` (K + L), `[` (M + ,), `]` (, + .) |
| `COMBO_TERM_WIDE` | `75` | `timeout-ms` (Enter, J + ;) | The widest span (index + pinky); the pinky lags the most, so the loosest window |
| `COMBO_IDLE` | `175` | `require-prior-idle-ms` (all combos) | Disable combos in the middle of a typing stream |

**Two-axis tuning**: a **false negative** (the combo fails to fire) is addressed by **raising `timeout-ms`**; a **false positive** (a roll trips the combo) is suppressed by **`require-prior-idle-ms`**. Because the idle gate disables every combo while a key was pressed within the last 175 ms, loosening `timeout-ms` does not reintroduce mid-typing misfires on bigrams like "io" or "ui".

These three tiers are the **current provisional values from on-device tuning** and may be adjusted as long-term use surfaces false positives. See [Combo Reference → Combo Misfire Tuning](combos.md#combo-misfire-tuning) for details.

## Layers

The keymap currently has **7 layers** (0–6):

| # | Name | Entry | Purpose |
|---|------|-------|---------|
| 0 | Base | Default | Letters, combos for modifiers/symbols/editing |
| 1 | Symbols | `mo 1` thumb (position 42) | Symbols, numbers, F-keys |
| 2 | Mix | `mo 2` thumb (position 45) | Function keys, number row, editing |
| 3 | Adjust | Conditional (Symbols + Mix simultaneously) | BT management, system controls |
| 4 | Navi | `lt 4 SPACE` thumb hold (position 43) | Arrow keys, navigation |
| 5 | NumPad | `mo 5` thumb hold (position 41) | Numeric keypad |
| 6 | Debug | `mo 6` within Navi or NumPad layer | Bluetooth pairing, bootloader |

The historical Combo layer (formerly index 6) has been removed, and the Debug layer was renumbered from 7 to 6.

## Conditional Layers

When both Symbols (layer 1) and Mix (layer 2) are active simultaneously, Adjust (layer 3) activates automatically.

```dts
conditional_layers {
    adjust_layer {
        if-layers = <2 1>;
        then-layer = <3>;
    };
};
```

## Encoder (Sensor Bindings)

Each layer can override encoder behavior. Layers without `sensor-bindings` fall back to lower layers.

| Layer | Left Encoder ↑/↓ | Right Encoder ↑/↓ |
|-------|-----------------|-------------------|
| Base | Volume Up / Down | Page Up / Page Down |
| Symbols | Cursor Right / Left | Cursor Down / Up |
| Mix | Ctrl+Tab / Ctrl+Shift+Tab (browser tab switch) | Volume Up / Down |
| Navi | End / Home | Page Down / Up |
| NumPad | KP_PLUS / KP_MINUS | KP_MULTIPLY / KP_DIVIDE |
| Adjust, Debug | (fallback to Base) | (fallback to Base) |

## Firmware Settings (`cornix.conf`)

| Setting | Value | Purpose |
|---------|-------|---------|
| `CONFIG_BT_CTLR_TX_PWR_PLUS_8` | `y` | Increase BT TX power for stable split connection |
| `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT` | `1800000` | Sleep after 30 min idle (saves battery) |
| `CONFIG_ZMK_KSCAN_DEBOUNCE_PRESS_MS` | `10` | Key press debounce |
| `CONFIG_ZMK_KSCAN_DEBOUNCE_RELEASE_MS` | `10` | Key release debounce |
| `CONFIG_BT_PERIPHERAL_PREF_MIN_INT` | `6` | BT connection interval min (7.5 ms) |
| `CONFIG_BT_PERIPHERAL_PREF_MAX_INT` | `12` | BT connection interval max (15 ms) |
| `CONFIG_ZMK_STUDIO` | `y` | Enable ZMK Studio (live keymap editing over USB) |
| `CONFIG_ZMK_STUDIO_LOCKING` | `n` | Disable studio locking (personal use) |
| `CONFIG_ZMK_USB_LOGGING` | `n` | Disable USB logging |

## ZMK Studio Support

This config builds with **ZMK Studio** support on the central (left) side. Connect the left half via USB and open <https://zmk.studio/> in Chrome or Edge to live-edit the keymap.

The `build.yaml` adds the `studio-rpc-usb-uart` snippet to `cornix_left//zmk`:

```yaml
include:
  - board: cornix_left//zmk
    snippet: studio-rpc-usb-uart
    artifact-name: cornix_left
```

**Note**: ZMK Studio can edit static keymap bindings (which key maps to which keycode) but **cannot edit behavior definitions** (`bs_del`, layer-taps, mod-morphs, encoder mappings). For those, edit `config/cornix.keymap` directly and rebuild.
