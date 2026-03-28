# コンボリファレンス

`config/cornix.keymap` で定義されている全コンボの一覧です。

全コンボに `require-prior-idle-ms = <150>` を設定しています。直前のキー入力から150ms経過していないとコンボが発動しないため、高速タイピング中の誤発動を防止します。

## Comboレイヤー（F長押し）

修飾キーと編集系コンボは**Comboレイヤー**（レイヤー7）専用で、F長押しで有効になります。通常タイピング中にI+OやJ+'が誤発動する問題を根本的に解消しています。

Comboレイヤー中は**定義した修飾キーとコンボ以外のキーは全て無効**（`&none`）です。Fを離すとBaseレイヤーに戻ります。

### 左手 — キーマップ直接割当

F（レイヤーキー）自身がコンボメンバーになるとコンボ解決と競合するため、コンボではなくレイヤー上のキーマップに直接割り当てています。

| キー | 動作 |
|------|------|
| A | Sticky 左Shift |
| S | Sticky 左Control |
| D | Sticky 左Alt |

### 右手 — レイヤー限定コンボ

| コンボ | キー | 動作 | timeout-ms |
|--------|------|------|-----------|
| Enter | J + ' | `RET` | 60 |
| Backspace / Delete | I + O | `BSPC`（Shift: `DEL`） | 60 |
| R-Ctrl | J + L | Sticky 右Control | |
| R-Shift | J + ; | Sticky 右Shift | |
| R-Alt | J + K | Sticky 右Alt | |

Sticky keyは**スタック可能**です：F長押し → A（Shift）→ S（Ctrl）→ F離して任意のキー → Ctrl+Shift+キーが送信されます。

## ナビゲーション・編集（Base/Winレイヤー）

| コンボ | キー | 動作 | レイヤー | timeout-ms |
|--------|------|------|----------|-----------|
| Escape | W + E | `ESC` | Base, Win | |
| Tab | W + R | `TAB` | Base, Win | |
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

## レイヤー切替（ホールド）

| コンボ | キー | レイヤー | timeout-ms |
|--------|------|----------|-----------|
| Layer 2 (Symbols) | C + X | シンボル | |
| Layer 2 (Symbols) | , + . | シンボル | |
| Layer 3 (Mix) | F + Z | ミックス | 60 |
| Layer 3 (Mix) | J + / | ミックス | 60 |

> **注意**: Keymap Editorは `timeout-ms` と `require-prior-idle-ms` を保持しません。GUIで編集した後にこれらの値を再追加する必要があります。
