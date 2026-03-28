# Combo Reference

All combos defined in `config/cornix.keymap`.

All combos have `require-prior-idle-ms = <150>` to prevent misfires during fast typing. Combos only trigger if 150ms has passed since the last keypress.

## Combo Layer (F hold)

Modifier and editing combos are restricted to the **Combo layer** (layer 7), activated by holding F. This eliminates misfires during normal typing — keys like I+O or J+' won't accidentally trigger while typing words.

While on the Combo layer, **all keys except the defined modifiers and combos are disabled** (`&none`). Releasing F returns to the Base layer.

### Left Hand — Direct Keymap Bindings

These are mapped directly on the Combo layer (not as combos), since F (the layer key) would conflict with combo resolution.

| Key | Action |
|-----|--------|
| A | Sticky Left Shift |
| S | Sticky Left Control |
| D | Sticky Left Alt |
| Z (hold) | Mix layer (layer 3) |

### Right Hand — Layer-Restricted Combos

| Combo | Keys | Action | timeout-ms |
|-------|------|--------|-----------|
| Backspace / Delete | I + O | `BSPC` (Shift: `DEL`) | 60 |
| R-Ctrl | J + L | Sticky Right Control | |
| R-Shift | J + ; | Sticky Right Shift | |
| R-Alt | J + K | Sticky Right Alt | |

Sticky keys **stack**: hold F, press A (Shift), then press S (Ctrl), then release F and press a key → sends Ctrl+Shift+key.

## Navigation & Editing (Base/Win layers)

| Combo | Keys | Action | Layers | timeout-ms |
|-------|------|--------|--------|-----------|
| Escape | W + E | `ESC` | Base, Win | |
| Tab | W + R | `TAB` | Base, Win | |
| Enter | J + ' | `RET` | Base, Combo | 60 |
| Space | G + B | `SPACE` | Base, Win | |
| Insert | J + O | `INS` | All | |
| IME Toggle | E + R | Alt + \` | All | |

## Brackets & Braces

| Combo | Keys | Action |
|-------|------|--------|
| `{` | T + G | Left Brace |
| `}` | Y + H | Right Brace |
| `(` | R + F | Left Parenthesis |
| `)` | U + J | Right Parenthesis |
| `<` | W + S | Less Than |
| `>` | O + L | Greater Than |
| `[` | E + D | Left Bracket |
| `]` | I + K | Right Bracket |

## Layer Activation (hold)

| Combo | Keys | Layer | timeout-ms |
|-------|------|-------|-----------|
| Layer 2 (Symbols) | C + X | Symbols | |
| Layer 2 (Symbols) | , + . | Symbols | |
| Layer 3 (Mix) | F + Z | Mix | 60 |
| Layer 3 (Mix) | J + / | Mix | 60 |

> **Note**: Keymap Editor does not preserve `timeout-ms` or `require-prior-idle-ms`. These values must be re-added after editing with the GUI.
