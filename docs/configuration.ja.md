# 設定リファレンス

このキーマップで使われている振る舞い (behavior)・マクロ・ファームウェア設定の詳細です。

## 設計思想

このキーマップは **urob 流「timeless」ホームロウ Mods (HRM)** と多数のコンボを組み合わせた設計です。修飾キー (Shift / Ctrl / Alt / GUI) は home 行のホームロウ Mods (`hm_l` / `hm_r`)、編集系キー・記号・括弧・リーダーキーはコンボで取得します。`require-prior-idle-ms = 150` ゲートで mod を速打から守り、コンボは独自のゲート (`require-prior-idle-ms = 175`) と短い `timeout-ms` で誤爆を防ぎます。

コンボの配置と使い方は [コンボリファレンス](combos.ja.md)、設計判断の経緯は [キーマップ設計ログ](keymap-design.ja.md) を参照してください。

## 振る舞い (Behavior)

### ホームロウ Mods: `hm_l` / `hm_r`

home 行で使う positional hold-tap です。タップで文字、(一拍置いた) ホールドで修飾キーを送出します。

```dts
hm_l: homerow_mods_left {
    compatible = "zmk,behavior-hold-tap";
    bindings = <&kp>, <&kp>;
    flavor = "balanced";
    tapping-term-ms = <280>;          // HM_TAPPING_TERM
    quick-tap-ms = <175>;             // HM_QUICK_TAP
    require-prior-idle-ms = <150>;    // HM_PRIOR_IDLE —「timeless」ゲート
    hold-trigger-key-positions = <KEYS_R KEYS_T>;  // 反対手 + 親指
    hold-trigger-on-release;
};
```

`hm_r` は同一定義で、発動を左手 (`KEYS_L KEYS_T`) に限定します。両者は **Shift も含め**同じタイミング定数を共有します。以前は Shift だけ `require-prior-idle-ms = 0` だったのが速打時の誤発火の真因で、HRM 再導入 (2026-06) 時に 150 ms へ統一しました。

home 行の割当 (小指 → 人差し指):

| 左 | Mod | 右 | Mod |
|----|-----|----|-----|
| A | LGUI | J | LCTRL |
| S | LALT | K | LSHFT |
| D | LSHFT | L | LALT |
| F | LCTRL | ; | LGUI |

`require-prior-idle-ms = 150` の意味: 直前のキー入力から 150 ms 以内に押されたキーは、ホールドしてもタップに倒れます。そのため速いロールや連打では修飾キーが一切出ず (レイテンシゼロ)、修飾キーは一拍置いた単独ホールドのみで発動します。`hold-trigger-key-positions` により mod は positional なので、home 行キーをホールドしたまま同手の文字を打っても文字が出ます。

非 positional な `hm` 振る舞い (`hold-trigger-key-positions` なし) も予備として残していますが、home 行自体は positional な `hm_l` / `hm_r` を使います。

### Mod-Morph: `bs_del`

Shift が押されているかどうかで異なるキーコードを発射します。

```dts
bs_del: bs_del {
    compatible = "zmk,behavior-mod-morph";
    bindings = <&kp BSPC>, <&kp DEL>;
    mods = <(MOD_LSFT|MOD_RSFT)>;
};
```

- **タップ**: Backspace
- **Shift + タップ**: Delete

Backspace コンボ (`cb_kp_bs`, I + O) の bindings で使用しています。(専用 Delete も U + I コンボに素の `&kp DEL` として存在します。)

### 記号 Mod-Morph: `cm_quote` / `cm_lpar` / `cm_rpar` / `cm_lbkt` / `cm_rbkt`

括弧・引用符コンボは `bs_del` と同じ Shift 連動 `mod-morph` 方式です。タップで一方の記号、Shift + タップで山括弧・波括弧版を送出します。

```dts
cm_quote: cm_quote {
    compatible = "zmk,behavior-mod-morph";
    bindings = <&kp SQT>, <&kp DQT>;   // ' / "
    mods = <(MOD_LSFT|MOD_RSFT)>;
};
```

| 振る舞い  | タップ | Shift + タップ | コンボ |
|-----------|--------|----------------|--------|
| `cm_quote` | `'` | `"` | J + L |
| `cm_lpar`  | `(` | `<` | J + K |
| `cm_rpar`  | `)` | `>` | K + L |
| `cm_lbkt`  | `[` | `{` | M + , |
| `cm_rbkt`  | `]` | `}` | , + . |

5 個とも `mods = <(MOD_LSFT|MOD_RSFT)>` (`bs_del` と同じ Shift トリガ) を共有します。

### リーダー / Tab Mod-Morph: `df_lead`

Leader コンボ (D + F) 自体が `mod-morph` です。修飾なしではリーダーキーを実行し、**Alt 保持中**は Tab を送出して Alt を残す (`keep-mods`) ため Alt+Tab になります。

```dts
df_lead: df_lead {
    compatible = "zmk,behavior-mod-morph";
    bindings = <&leader>, <&kp TAB>;
    mods = <(MOD_LALT|MOD_RALT)>;
    keep-mods = <(MOD_LALT|MOD_RALT)>;  // Alt を残して Alt+Tab でウィンドウ循環
};
```

- **タップ (Alt なし)**: リーダーモードに入る (`&leader`)。
- **Alt + タップ**: Alt+Tab。Alt を保持したまま D + F を連打するとウィンドウを循環。

### レイヤータップ: `lt`

ホールドでレイヤー起動、タップでキーコード送出。

```dts
&lt 4 SPACE     // タップ: SPACE、ホールド: Navi レイヤー
&lt 1 BACKSPACE // タップ: BSPC、ホールド: Symbols レイヤー
```

NumPad (layer 5) をホールドする親指は、layer-tap ではなく素の `&mo 5` (ホールド専用) になりました。タップしても Esc は出ません。Esc は W + E コンボへ完全に移行しています。

### レイヤータップ (tap-preferred): `lt_g`

タップを優先する layer-tap で、`G` キー (`lt_g 4 G`) に使用します (タップ = `G`、ホールド = Navi レイヤー)。HRM の定数を継承せず独自の定数 (`tapping-term-ms = 250` / `require-prior-idle-ms = 70`) を保持します。実機検証でこの操作感が最も自然だったためです。

```dts
lt_g: layer_tap_g {
    compatible = "zmk,behavior-hold-tap";
    bindings = <&mo>, <&kp>;
    flavor = "tap-preferred";
    tapping-term-ms = <250>;
    quick-tap-ms = <200>;
    require-prior-idle-ms = <70>;
};
```

### リーダーキー: `leader`

urob の [zmk-leader-key](https://github.com/urob/zmk-leader-key) モジュール (west モジュール — ZMK 公式 `main` 未収録機能) によるモーダルなリーダーキーです。Leader コンボ (D + F、`df_lead` mod-morph 経由) をタップしてリーダーモードに入り、後続のキー列でその割当を発火します。このモジュール版は **timeout / layers / immediate-trigger を持たず**、有効な並びが揃うまで待機し、無効キーで中断します。

```dts
leader: leader {
    compatible = "zmk,behavior-leader-key";
    ldr_cc { sequence = <C C>; bindings = <&ctrl_ctrl>; };    // c c → Ctrl 二連打
    ldr_ss { sequence = <S S>; bindings = <&shift_shift>; };  // s s → Shift 二連打
};
```

## マクロ

### IDE ジェスチャ: `ctrl_ctrl` / `shift_shift`

同一修飾を二連打して host に double-tap として検出させます (IntelliJ の "Run Anything" / "Search Everywhere" 等の IDE ショートカット向け)。リーダーシーケンス `c c` / `s s` から呼び出します。

```dts
ctrl_ctrl: ctrl_ctrl {
    compatible = "zmk,behavior-macro";
    wait-ms = <40>;
    tap-ms = <40>;
    bindings = <&kp LCTRL>, <&kp LCTRL>;
};
```

`shift_shift` は `LSHFT` 版で同一構造。host が "double tap" と認識する間隔は IDE 依存のため、ジェスチャが拾われない場合は `wait-ms` / `tap-ms` を調整してください。

## コンボのチューニング定数

`config/cornix.keymap` の全コンボは、誤爆耐性を左右する少数の `#define` 定数を共有します：

`timeout-ms` の窓は指のジオメトリで **三段階**にしています。`require-prior-idle-ms` は全コンボ一律 (`175`) です。

| 定数 | 値 | 適用先 | 用途 |
|------|----|--------|------|
| `COMBO_TERM` | `30` | `timeout-ms` (同指の縦コンボ) | 1 本の指で上下 2 キーを叩くため指間ズレが無く、短い窓で成立する。かつ同指縦ペアは構造上ロール誤爆に強いので、短い timeout でも誤発火耐性を損なわない |
| `COMBO_TERM_HORIZ` | `60` | `timeout-ms` (別指の横並びコンボ) | 隣接キーを**別々の指**で押すと指のズレ (長い中指が先行・小指が遅れる) が出るため、`30` では 2 キーが揃わず**不発 (FN)** になった。`60` に緩めた。対象: Delete (U + I)、Backspace (I + O)、Insert (O + J)、`'` (J + L)、`(` (J + K)、`)` (K + L)、`[` (M + ,)、`]` (, + .) |
| `COMBO_TERM_WIDE` | `75` | `timeout-ms` (Enter, J + ;) | 最もスパンが広く (index + 小指)、小指が最も遅れるため最も緩い窓 |
| `COMBO_IDLE` | `175` | `require-prior-idle-ms` (全コンボ) | 打鍵流中のコンボを無効化 |

**2 軸のチューニング**: **不発 (FN)** は **`timeout-ms` を上げて**対処し、**誤発火 (FP)** は **`require-prior-idle-ms`** で抑える、という別軸の整理です。idle ゲートが直前 175 ms 以内に押されたキーがある間は全コンボを無効化するため、`timeout-ms` を緩めても "io" / "ui" 等の bigram での打鍵流中の誤爆は再発しません。

この三段階は**実機チューニングで決めた現時点の暫定値**で、長期使用での誤発火 (FP) 観察に応じて調整しうる値です。詳細は [コンボリファレンス → コンボ誤爆対策チューニング](combos.ja.md#コンボ誤爆対策チューニング) を参照。

## レイヤー構成

現状は **7 レイヤー** (0–6) 構成：

| # | 名前 | 入口 | 用途 |
|---|------|------|------|
| 0 | Base | デフォルト | 文字入力、修飾キー・記号・編集系のコンボ |
| 1 | Symbols | 親指 `mo 1` (位置 42) | 記号・数字・F キー |
| 2 | Mix | 親指 `mo 2` (位置 45) | F キー、数字行、編集系 |
| 3 | Adjust | Conditional (Symbols + Mix 同時押し) | BT 管理、システムコントロール |
| 4 | Navi | 親指 `lt 4 SPACE` ホールド (位置 43) | カーソル移動 |
| 5 | NumPad | 親指 `mo 5` ホールド (位置 41) | テンキー |
| 6 | Debug | Navi/NumPad 内の `mo 6` | Bluetooth ペアリング、ブートローダー |

旧 Combo レイヤー (旧 index 6) は廃止済み。Debug は 7 → 6 に番号を繰り上げています。

## 条件付きレイヤー

Symbols (layer 1) と Mix (layer 2) が同時にアクティブな時、Adjust (layer 3) が自動的に起動します。

```dts
conditional_layers {
    adjust_layer {
        if-layers = <2 1>;
        then-layer = <3>;
    };
};
```

## ロータリーエンコーダ (Sensor Bindings)

各レイヤーで `sensor-bindings` を上書きできます。未定義のレイヤーは下位レイヤーへフォールバック。

| レイヤー | 左つまみ ↑/↓ | 右つまみ ↑/↓ |
|---------|-------------|-------------|
| Base    | 音量 Up / Down | Page Up / Down |
| Symbols | カーソル 右 / 左 | カーソル 下 / 上 |
| Mix     | Ctrl+Tab / Ctrl+Shift+Tab (ブラウザタブ切替) | 音量 Up / Down |
| Navi    | End / Home | Page Down / Up |
| NumPad  | KP_PLUS / KP_MINUS | KP_MULTIPLY / KP_DIVIDE |
| Adjust / Debug | (Base へフォールバック) | (Base へフォールバック) |

## ファームウェア設定 (`cornix.conf`)

| 設定 | 値 | 用途 |
|------|----|------|
| `CONFIG_BT_CTLR_TX_PWR_PLUS_8` | `y` | BT 送信出力を増加 (分割接続の安定化) |
| `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT` | `1800000` | 30 分でスリープ (省電力) |
| `CONFIG_ZMK_KSCAN_DEBOUNCE_PRESS_MS` | `10` | キー押下デバウンス |
| `CONFIG_ZMK_KSCAN_DEBOUNCE_RELEASE_MS` | `10` | キーリリースデバウンス |
| `CONFIG_BT_PERIPHERAL_PREF_MIN_INT` | `6` | BT 接続インターバル最小 (7.5 ms) |
| `CONFIG_BT_PERIPHERAL_PREF_MAX_INT` | `12` | BT 接続インターバル最大 (15 ms) |
| `CONFIG_ZMK_STUDIO` | `y` | ZMK Studio 有効化 (USB 経由のライブ keymap 編集) |
| `CONFIG_ZMK_STUDIO_LOCKING` | `n` | Studio ロック無効 (個人利用) |
| `CONFIG_ZMK_USB_LOGGING` | `n` | USB ログ無効 |

## ZMK Studio 対応

このリポジトリは **ZMK Studio** 対応ファームウェアを Central (左半身) でビルドします。左半身を USB 接続し、Chrome または Edge で <https://zmk.studio/> を開くとライブで keymap を編集できます。

`build.yaml` に `studio-rpc-usb-uart` snippet を追加することで Studio 用 RPC 通信を有効化：

```yaml
include:
  - board: cornix_left//zmk
    snippet: studio-rpc-usb-uart
    artifact-name: cornix_left
```

**注意**: ZMK Studio は静的な keymap (どのキーがどのキーコードを発射するか) を編集できますが、**振る舞い定義 (`bs_del` / レイヤータップ / mod-morph / エンコーダのマッピング) は編集できません**。これらは `config/cornix.keymap` を直接編集して再ビルドする必要があります。
