# Combo Reference

All combos defined in `config/cornix.keymap`.

All combos have `require-prior-idle-ms = <150>` to prevent misfires during fast typing. Combos only trigger if 150ms has passed since the last keypress.

## Navigation & Editing

| Combo | Keys | Action | Layers | timeout-ms |
|-------|------|--------|--------|-----------|
| Escape | W + E | `ESC` | Base, Win | |
| Tab | W + R | `TAB` | Base, Win | |
| Enter | J + ' | `RET` | Base, Win | 45 |
| Backspace / Delete | I + O | `BSPC` (Shift: `DEL`) | Base, Win | 60 |
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

## Modifiers (Sticky Key)

Modifier combos use sticky keys (`&sk`). Press the combo, release, then press the next key — the modifier applies to that one keypress only.

Sticky keys **stack**: press F+S (Ctrl), then F+A (Shift), then any key → sends Ctrl+Shift+key. This enables one-handed modifier combinations.

### Left Hand (anchor: F)

| Combo | Keys | Action |
|-------|------|--------|
| L-Ctrl | F + S | Sticky Left Control |
| L-Shift | F + A | Sticky Left Shift |
| L-Alt | F + D | Sticky Left Alt |

### Right Hand (anchor: J)

| Combo | Keys | Action |
|-------|------|--------|
| R-Ctrl | J + L | Sticky Right Control |
| R-Shift | J + ; | Sticky Right Shift |
| R-Alt | J + K | Sticky Right Alt |

## Layer Activation (hold)

| Combo | Keys | Layer | timeout-ms |
|-------|------|-------|-----------|
| Layer 2 (Symbols) | C + X | Symbols | |
| Layer 2 (Symbols) | , + . | Symbols | |
| Layer 3 (Mix) | F + Z | Mix | 60 |
| Layer 3 (Mix) | J + / | Mix | 60 |

> **Note**: Keymap Editor does not preserve `timeout-ms` or `require-prior-idle-ms`. These values must be re-added after editing with the GUI.
