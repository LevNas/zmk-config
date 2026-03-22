# Configuration Reference

Detailed explanation of behaviors, macros, and firmware settings used in this keymap.

## Behaviors

### Homerow Mods (Positional)

Based on [urob's timeless homerow mods](https://github.com/urob/zmk-config/tree/main#timeless-homerow-mods). Modifiers are activated by holding home row keys, but only trigger when the opposite hand types a key.

| Behavior | Usage | Description |
|----------|-------|-------------|
| `hm_l` | Left hand modifiers | Triggers only with right-hand keys |
| `hm_r` | Right hand modifiers | Triggers only with left-hand keys |
| `hm_shift_l` | Left shift | Faster tapping term (200ms vs 250ms) |
| `hm_shift_r` | Right shift | Faster tapping term (200ms vs 250ms) |
| `hm` | Non-positional | No hand restriction, used for general hold-tap |

**Key parameters:**

| Parameter | Value | Purpose |
|-----------|-------|---------|
| `tapping-term-ms` | 250ms (shift: 200ms) | How long to hold before modifier activates |
| `quick-tap-ms` | 200ms | Tap-tap-hold repeats the tap key |
| `require-prior-idle-ms` | 70ms | Prevents accidental activation during fast typing |
| `hold-trigger-on-release` | enabled | More responsive modifier activation |

**Example** (Win layer):

```dts
&hm_l LCTRL A    // Tap: A, Hold: Left Control
&hm_shift_r RSHFT J  // Tap: J, Hold: Right Shift
```

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

Used in the I+O combo.

### Layer Tap: `lt`

Hold to activate a layer, tap to send a keycode.

```dts
&lt 6 ESCAPE   // Tap: Escape, Hold: NumPad layer
&lt 5 SPACE    // Tap: Space, Hold: Navi layer
&lt 2 BACKSPACE // Tap: Backspace, Hold: Symbols layer
```

## Macros

### IME Toggle: `my_macro_zenhan`

Sends Alt + `` ` `` to toggle IME on/off (Windows, US keyboard layout).

```dts
my_macro_zenhan: my_macro_zenhan {
    compatible = "zmk,behavior-macro";
    bindings =
        <&macro_press>, <&kp LEFT_ALT>,
        <&macro_tap>, <&kp GRAVE>,
        <&macro_release>, <&kp LEFT_ALT>;
};
```

Used in the E+R combo.

## Conditional Layers

When both Layer 2 (Symbols) and Layer 3 (Mix) are active simultaneously, Layer 4 (Adjust) activates automatically.

```dts
conditional_layers {
    adjust_layer {
        if-layers = <3 2>;
        then-layer = <4>;
    };
};
```

Layer 4 (Adjust) provides BT management, layer toggles, and system controls.

### Sticky Key Modifiers

Modifier combos use `&sk` (sticky key) instead of `&kp`. This enables one-handed modifier stacking.

```dts
cb_kp_l-ctrl {
    bindings = <&sk LCTRL>;
    key-positions = <16 14>;    // F + S
    require-prior-idle-ms = <150>;
};
```

- **Press combo, release, press next key**: Modifier applies to one keypress only
- **Stack multiple**: F+S (Ctrl) → F+A (Shift) → key = Ctrl+Shift+key
- **Auto-release**: Modifier deactivates after the next keypress

### Layer Tap for G key: `lt_g`

A special layer-tap behavior for the G key. Uses `tap-preferred` flavor and `require-prior-idle-ms` so that fast typing (e.g. "ga", "gi", "gg") always sends the letter, and only a deliberate long press activates the Navi layer.

```dts
lt_g: layer_tap_g {
    compatible = "zmk,behavior-hold-tap";
    bindings = <&mo>, <&kp>;
    flavor = "tap-preferred";
    tapping-term-ms = <HM_TAPPING_TERM>;
    quick-tap-ms = <200>;
    require-prior-idle-ms = <HM_PRIOR_IDLE>;
};
```

- **Tap**: G
- **Hold** (250ms+, after idle): Navi layer (layer 5)
- **Fast typing**: Always sends G (no layer activation)

## Firmware Settings (`cornix.conf`)

| Setting | Value | Purpose |
|---------|-------|---------|
| `CONFIG_BT_CTLR_TX_PWR_PLUS_8` | `y` | Increase BT TX power for stable split connection |
| `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT` | `900000` | Sleep after 15 min idle (saves battery) |
| `CONFIG_ZMK_KSCAN_DEBOUNCE_PRESS_MS` | `10` | Key press debounce (prevent chattering) |
| `CONFIG_ZMK_KSCAN_DEBOUNCE_RELEASE_MS` | `10` | Key release debounce (prevent stuck keys) |
| `CONFIG_BT_PERIPHERAL_PREF_MIN_INT` | `6` | BT connection interval min (7.5ms, stability) |
| `CONFIG_BT_PERIPHERAL_PREF_MAX_INT` | `12` | BT connection interval max (15ms, stability) |
