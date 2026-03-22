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

## Firmware Settings (`cornix.conf`)

| Setting | Value | Purpose |
|---------|-------|---------|
| `CONFIG_BT_CTLR_TX_PWR_PLUS_8` | `y` | Increase BT TX power for stable split connection |
| `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT` | `900000` | Sleep after 15 min idle (saves battery) |
