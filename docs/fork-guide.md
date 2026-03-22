# Fork Guide

How to fork this repository and customize the keymap for your own Cornix LP.

## Quick Start

1. **Fork** this repository on GitHub
2. Edit `config/cornix.keymap` to customize your keymap
3. Push changes — GitHub Actions will build the firmware automatically
4. Download `.uf2` files from the Actions tab

## Editing the Keymap

### Option 1: Keymap Editor (GUI)

The easiest way to edit your keymap is with [Keymap Editor](https://nickcoutsos.github.io/keymap-editor/).

1. Open [Keymap Editor](https://nickcoutsos.github.io/keymap-editor/)
2. Grant access to your forked repository
3. Select your fork
4. Edit keys visually and commit changes

The layout definition (`config/cornix.json`) is included, so Keymap Editor will display the Cornix LP layout automatically.

> **Note**: Keymap Editor has limited support for preprocessor macros (`#define`, `#include`). Some advanced features like homerow mods may not render correctly but will still build fine.

### Option 2: Edit `.keymap` directly

Edit `config/cornix.keymap` in any text editor. Refer to the [ZMK documentation](https://zmk.dev/docs) for keymap syntax.

## Key Files

| File | Purpose |
|------|---------|
| `config/cornix.keymap` | Keymap definition (layers, combos, behaviors) |
| `config/cornix.conf` | Firmware configuration (BT power, sleep timeout) |
| `config/cornix.json` | Physical layout for Keymap Editor |
| `config/includes/cornix54.h` | Key position definitions for combos |
| `config/west.yml` | ZMK and module dependencies |
| `build.yaml` | GitHub Actions build matrix |

## Adding Combos

Combos are defined in the `combos` section of `cornix.keymap`. Use key position numbers from `config/includes/cornix54.h`:

```
╭────────────────────────╮               ╭────────────────────────╮
│  0   1   2   3   4   5 │               │  6   7   8   9  10  11 │
│ 12  13  14  15  16  17 ╰──╮         ╭──╯ 18  19  20  21  22  23 │
│ 24  25  26  27  28  29  30╰────┬────╯31  32  33  34  35  36  37 │
│ 38  39  40      41  42  43     │     44  45  46      47  48  49 │
╰────────────────────────────────┴────────────────────────────────╯
```

Example combo:

```dts
cb_kp_esc {
    bindings = <&kp ESC>;
    key-positions = <2 3>;    // W + E
    layers = <0 1>;           // Only on Base and Win layers
};
```

## Changing BT / Power Settings

Edit `config/cornix.conf`:

```ini
# Increase BT TX power for better split connection
CONFIG_BT_CTLR_TX_PWR_PLUS_8=y

# Idle sleep timeout in ms (default: 900000 = 15 min)
CONFIG_ZMK_IDLE_SLEEP_TIMEOUT=900000
```

## Build Notes

This repository pins the ZMK CI workflow to a specific commit to avoid a [known compatibility issue](https://github.com/hitsmaxft/zmk-keyboard-cornix/issues/26) with ZMK's board variant check (Zephyr 4.1). Once the upstream Cornix board definition adds ZMK variant support, the workflow can be updated to `@main`.
