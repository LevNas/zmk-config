# zmk-config

[Cornix LP](https://github.com/hitsmaxft/zmk-keyboard-cornix) 分割キーボード（ロータリーエンコーダ付き）の ZMK ファームウェア設定です。標準ファームウェア (RMK) との違いは [なぜ ZMK？](docs/why-zmk.ja.md) を参照してください。

[English](README.md)

## 設計

このキーマップは **urob 流「timeless」ホームロウ Mods (HRM)** と少数のコンボを組み合わせた設計です。home 行に修飾キー (GUI / Alt / Shift / Ctrl) を載せ、編集系キー (Esc / Tab / Enter / Backspace 等) とリーダーキーをコンボで扱います。`require-prior-idle-ms` ゲートにより速打中は mod が一切出ないため、ロールや高速連打で修飾キーが暴発しません。USB 経由のライブ編集に対応する **ZMK Studio** ファームウェアとしてもビルドされます。

> 以前のリビジョンでは HM を全撤廃して修飾コンボに一本化していましたが、2026-06 に撤回しました。真因は HM 自体ではなく、Shift 用 hold-tap の `require-prior-idle-ms = 0` 設定でした。全 mod を `150 ms` に統一して誤発火が解消し、HRM を再導入しています。

詳細は以下を参照：
- [コンボリファレンス](docs/combos.ja.md)
- [設定リファレンス](docs/configuration.ja.md)
- [キーマップ設計ログ](docs/keymap-design.ja.md) — 設計判断の経緯

## キーマップ

![Cornix LP Keymap](docs/keymap.svg)

## クイックリファレンス (試打用早見表)

### ホームロウ Mods

home 行のキーをホールドすると修飾キー、タップ (または素早いロール) で文字になります。`require-prior-idle-ms = 150` ゲートにより、一拍置いた単独ホールドのみが mod として発動します。

| 左 | Mod | | 右 | Mod |
|----|-----|-|----|-----|
| A | ⌘ GUI | | J | ⌃ Ctrl |
| S | ⌥ Alt | | K | ⇧ Shift |
| D | ⇧ Shift | | L | ⌥ Alt |
| F | ⌃ Ctrl | | ; | ⌘ GUI |

複数を同時ホールドすれば修飾を重ねられます。**同じ手でも有効** (例: F + D ホールド = Ctrl+Shift)。`hold-trigger-on-release` が positional 判定を離鍵まで遅らせるため、**ホールドしたキーは mod になり、素早い同手ロール (タップ) は通常の文字入力のまま**で誤爆しません。`G` ホールド = Navi レイヤー、`H` は素のキー。

### コンボ (2 キー同時押し)

全コンボは発動前に一拍の idle を要求する (`require-prior-idle-ms = 175`) ため、速打中のロールでは誤爆しません。同時押しの窓 (`timeout-ms`) は指のジオメトリで三段階にチューニングしています: 同指の縦ペア (記号列) は短い `30`、別指の横並びペア (Delete / Backspace / Insert / 括弧・引用符) は `60`、最もスパンの広い index+小指の Enter (J + ;) は `75`。全一覧とチューニングの詳細は [コンボリファレンス](docs/combos.ja.md) を参照。

**編集 / ナビ**

| コンボ | キー | 動作 |
|--------|------|------|
| Esc | W + E | Escape |
| Tab | S + D | Tab |
| Delete | U + I | Delete |
| Backspace | I + O | Backspace (Shift + 同時 = Delete) |
| Insert | O + J | Insert |
| Enter | J + ; | Enter (timeout やや緩め) |
| Leader | D + F | リーダーモード開始 — ただし **Alt + D + F = Alt+Tab** (下記参照) |

**記号** (Shift 不要)

| `@` | `#` | `$` | `%` | `^` | `+` | `*` | `&` |
|-----|-----|-----|-----|-----|-----|-----|-----|
| W+S | E+D | R+F | T+G | Y+H | U+J | I+K | O+L |

| `` ` `` | `\` | `=` | `~` | `_` | `-` | `/` | `\|` |
|---------|-----|-----|-----|-----|-----|-----|------|
| S+X | D+C | F+V | G+B | H+N | J+M | K+, | L+. |

**括弧 / 引用符** (Shift で山括弧・波括弧に変化)

| コンボ | キー | タップ | Shift |
|--------|------|--------|-------|
| `'` | J + L | `'` | `"` |
| `(` | J + K | `(` | `<` |
| `)` | K + L | `)` | `>` |
| `[` | M + , | `[` | `{` |
| `]` | , + . | `]` | `}` |

**Insert / Delete 派生**

| コンボ | キー | 動作 |
|--------|------|------|
| Ctrl+Insert | X + C | コピー (端末流) |
| Shift+Insert | C + V | ペースト (端末流) |
| Shift+Delete | X + V | カット (端末流) |

### リーダーシーケンス

**Leader** コンボ (D + F) をタップしてからシーケンスを入力します。(D + F を Alt 保持中に押すと、リーダーモードに入らず Alt+Tab を送出します。Alt を保持したまま D + F を連打するとウィンドウを循環できます。)

| シーケンス | 動作 |
|------------|------|
| `c` `c` | Ctrl 二連打 (例: IDE の "Run Anything") |
| `s` `s` | Shift 二連打 (例: IDE の "Search Everywhere") |

## ビルド

GitHub に push すると自動で GitHub Actions がビルドを実行します。
[最新の Actions ラン](https://github.com/LevNas/zmk-config/actions) から `.uf2` ファイルをダウンロードできます。

### フラッシュ方法

1. キーボード半分を UF2 ブートローダーモードにする (リセットボタンをダブルタップ)
2. 左側に `cornix_left.uf2`、右側に `cornix_right.uf2` をコピー
3. 別のファームウェアから切り替える場合は、先に両側に `reset.uf2` をフラッシュして BT ボンディング情報をクリア

### ZMK Studio (ライブ編集)

フラッシュ後、**左半身**を USB ケーブルで PC に接続し、Chrome または Edge で <https://zmk.studio/> を開くとライブで keymap を編集できます。
注意: Studio は振る舞い定義 (mod-morph / レイヤータップ / エンコーダマッピング) は編集できません。それらはソース編集 + 再ビルドが必要です。

## カスタマイズ

自分のキーマップを作りたい場合は、GitHub で **「Use this template」** をクリックしてリポジトリを作成してください。詳しくは [セットアップガイド](docs/fork-guide.ja.md) を参照。

## ハードウェア

- ボード: Cornix LP (分割、nRF52840)
- ロータリーエンコーダ: 2 個 (左: 音量 / 右: Page Up・Down (Base レイヤー時)、レイヤー別の上書きあり)
- ドングルなし

## 謝辞

- [hitsmaxft](https://github.com/hitsmaxft) — [Cornix LP](https://github.com/hitsmaxft/zmk-keyboard-cornix) のボード設計と ZMK ボード定義
- [urob](https://github.com/urob) — [zmk-helpers](https://github.com/urob/zmk-helpers) マクロ、[timeless homerow mods](https://github.com/urob/zmk-config#timeless-homerow-mods) の設計、[zmk-leader-key](https://github.com/urob/zmk-leader-key) モジュール
- [ZMK Contributors](https://zmk.dev/) — ZMK ファームウェアと ZMK Studio
