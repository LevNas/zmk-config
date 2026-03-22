# コンボリファレンス

`config/cornix.keymap` で定義されている全コンボの一覧です。

## ナビゲーション・編集

| コンボ | キー | 動作 | レイヤー | timeout-ms |
|--------|------|------|----------|-----------|
| Escape | W + E | `ESC` | Base, Win | |
| Tab | W + R | `TAB` | Base, Win | |
| Enter | J + ' | `RET` | Base, Win | 60 |
| Backspace / Delete | I + O | `BSPC`（Shift: `DEL`） | Base, Win | 45 |
| Space | G + B | `SPACE` | Base, Win | |
| Insert | J + O | `INS` | 全レイヤー | |
| IME切替 | E + R | Alt + `` ` `` | 全レイヤー | |

## 括弧・ブレース

| コンボ | キー | 動作 |
|--------|------|------|
| `{` | T + G | 左波括弧 |
| `}` | Y + H | 右波括弧 |
| `(` | R + F | 左丸括弧 |
| `)` | U + J | 右丸括弧 |
| `<` | W + S | 小なり |
| `>` | O + L | 大なり |
| `[` | E + D | 左角括弧 |
| `]` | I + K | 右角括弧 |

## 修飾キー（左手、アンカー: F）

| コンボ | キー | 動作 |
|--------|------|------|
| L-Ctrl | F + S | 左Control |
| L-Shift | F + A | 左Shift |
| L-Alt | F + D | 左Alt |
| L-Ctrl + L-Alt | F + S + D | 左Control + 左Alt |
| L-Ctrl + L-Shift | F + A + S | 左Control + 左Shift |

## 修飾キー（右手、アンカー: J）

| コンボ | キー | 動作 |
|--------|------|------|
| R-Ctrl | J + L | 右Control |
| R-Shift | J + ; | 右Shift |
| R-Alt | J + K | 右Alt |
| R-Ctrl + R-Alt | J + K + L | 右Control + 右Alt |
| R-Ctrl + R-Shift | J + L + ; | 右Control + 右Shift |

## レイヤー切替（ホールド）

| コンボ | キー | レイヤー | timeout-ms |
|--------|------|----------|-----------|
| Layer 2 (Symbols) | C + X | シンボル | |
| Layer 2 (Symbols) | , + . | シンボル | |
| Layer 3 (Mix) | F + Z | ミックス | 60 |
| Layer 3 (Mix) | J + / | ミックス | 60 |

> **注意**: 離れたキーのコンボ（F+Z, J+/, J+', I+O）には `timeout-ms` を延長しています。Keymap Editorは `timeout-ms` を保持しないため、GUIで編集した後に再追加が必要です。
