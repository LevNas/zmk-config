# Combo Reference

All combos defined in `config/cornix.keymap`.

## Navigation & Editing

| Combo | Keys | Action | Layers | timeout-ms |
|-------|------|--------|--------|-----------|
| Escape | W + E | `ESC` | Base, Win | |
| Tab | W + R | `TAB` | Base, Win | |
| Enter | J + ' | `RET` | Base, Win | 60 |
| Backspace / Delete | I + O | `BSPC` (Shift: `DEL`) | Base, Win | 45 |
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

## Modifiers (Left Hand, anchor: F)

| Combo | Keys | Action |
|-------|------|--------|
| L-Ctrl | F + S | Left Control |
| L-Shift | F + A | Left Shift |
| L-Alt | F + D | Left Alt |
| L-Ctrl + L-Alt | F + S + D | Left Control + Left Alt |
| L-Ctrl + L-Shift | F + A + S | Left Control + Left Shift |

## Modifiers (Right Hand, anchor: J)

| Combo | Keys | Action |
|-------|------|--------|
| R-Ctrl | J + L | Right Control |
| R-Shift | J + ; | Right Shift |
| R-Alt | J + K | Right Alt |
| R-Ctrl + R-Alt | J + K + L | Right Control + Right Alt |
| R-Ctrl + R-Shift | J + L + ; | Right Control + Right Shift |

## Layer Activation (hold)

| Combo | Keys | Layer | timeout-ms |
|-------|------|-------|-----------|
| Layer 2 (Symbols) | C + X | Symbols | |
| Layer 2 (Symbols) | , + . | Symbols | |
| Layer 3 (Mix) | F + Z | Mix | 60 |
| Layer 3 (Mix) | J + / | Mix | 60 |

> **Note**: Distant key combos (F+Z, J+/, J+', I+O) have extended `timeout-ms` to accommodate the finger travel distance. Keymap Editor does not preserve `timeout-ms`, so these values must be re-added after editing with the GUI.
