# コンボリファレンス

`config/cornix.keymap` で定義されている全コンボの一覧です。

全コンボに `require-prior-idle-ms = <150>` を設定しています。直前のキー入力から150ms経過していないとコンボが発動しないため、高速タイピング中の誤発動を防止します。

## ナビゲーション・編集

| コンボ | キー | 動作 | レイヤー | timeout-ms |
|--------|------|------|----------|-----------|
| Escape | W + E | `ESC` | Base, Win | |
| Tab | W + R | `TAB` | Base, Win | |
| Enter | J + ' | `RET` | Base, Win | 45 |
| Backspace / Delete | I + O | `BSPC`（Shift: `DEL`） | Base, Win | 60 |
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

## 修飾キー（Sticky Key）

修飾キーコンボはsticky key（`&sk`）を使用しています。コンボを押して離し、次の1キーにだけ修飾キーが適用されます。

Sticky keyは**スタック可能**です：F+S（Ctrl）→ F+A（Shift）→ 任意のキー → Ctrl+Shift+キーが送信されます。片手で複数の修飾キーを組み合わせられます。

### 左手（アンカー: F）

| コンボ | キー | 動作 |
|--------|------|------|
| L-Ctrl | F + S | Sticky 左Control |
| L-Shift | F + A | Sticky 左Shift |
| L-Alt | F + D | Sticky 左Alt |

### 右手（アンカー: J）

| コンボ | キー | 動作 |
|--------|------|------|
| R-Ctrl | J + L | Sticky 右Control |
| R-Shift | J + ; | Sticky 右Shift |
| R-Alt | J + K | Sticky 右Alt |

## レイヤー切替（ホールド）

| コンボ | キー | レイヤー | timeout-ms |
|--------|------|----------|-----------|
| Layer 2 (Symbols) | C + X | シンボル | |
| Layer 2 (Symbols) | , + . | シンボル | |
| Layer 3 (Mix) | F + Z | ミックス | 60 |
| Layer 3 (Mix) | J + / | ミックス | 60 |

> **注意**: Keymap Editorは `timeout-ms` と `require-prior-idle-ms` を保持しません。GUIで編集した後にこれらの値を再追加する必要があります。
