# 設定リファレンス

キーマップで使用しているビヘイビア、マクロ、ファームウェア設定の詳細です。

## ビヘイビア

### Homerow Mods（ポジショナル）

[urobのtimeless homerow mods](https://github.com/urob/zmk-config/tree/main#timeless-homerow-mods) をベースにしています。ホーム行のキーを長押しすると修飾キーになりますが、反対側の手でキーを押した場合のみ発動します。

| ビヘイビア | 用途 | 説明 |
|------------|------|------|
| `hm_l` | 左手の修飾キー | 右手のキーでのみ発動 |
| `hm_r` | 右手の修飾キー | 左手のキーでのみ発動 |
| `hm_shift_l` | 左Shift | タッピングタームが短い（200ms） |
| `hm_shift_r` | 右Shift | タッピングタームが短い（200ms） |
| `hm` | 非ポジショナル | 手の制約なし、汎用のhold-tap |

**主要パラメータ:**

| パラメータ | 値 | 目的 |
|------------|------|------|
| `tapping-term-ms` | 250ms（Shift: 200ms） | 修飾キーが発動するまでの長押し時間 |
| `quick-tap-ms` | 200ms | タップ→タップ→ホールドでタップキーをリピート |
| `require-prior-idle-ms` | 70ms | 高速タイピング時の誤発動を防止 |
| `hold-trigger-on-release` | 有効 | 修飾キーの応答性を向上 |

**使用例**（Winレイヤー）:

```dts
&hm_l LCTRL A    // タップ: A、ホールド: 左Control
&hm_shift_r RSHFT J  // タップ: J、ホールド: 右Shift
```

### Mod-Morph: `bs_del`

Shiftの有無で異なるキーコードを送信します。

```dts
bs_del: bs_del {
    compatible = "zmk,behavior-mod-morph";
    bindings = <&kp BSPC>, <&kp DEL>;
    mods = <(MOD_LSFT|MOD_RSFT)>;
};
```

- **タップ**: Backspace
- **Shift + タップ**: Delete

I+Oコンボで使用しています。

### Layer Tap: `lt`

ホールドでレイヤーを有効化、タップでキーコードを送信します。

```dts
&lt 6 ESCAPE   // タップ: Escape、ホールド: NumPadレイヤー
&lt 5 SPACE    // タップ: Space、ホールド: Naviレイヤー
&lt 2 BACKSPACE // タップ: Backspace、ホールド: Symbolsレイヤー
```

## マクロ

### IME切替: `my_macro_zenhan`

Alt + `` ` `` を送信してIMEのオン/オフを切り替えます（Windows、USキーボードレイアウト用）。

```dts
my_macro_zenhan: my_macro_zenhan {
    compatible = "zmk,behavior-macro";
    bindings =
        <&macro_press>, <&kp LEFT_ALT>,
        <&macro_tap>, <&kp GRAVE>,
        <&macro_release>, <&kp LEFT_ALT>;
};
```

E+Rコンボで使用しています。

## 条件レイヤー

Layer 2（Symbols）とLayer 3（Mix）を同時に有効にすると、Layer 4（Adjust）が自動的に有効になります。

```dts
conditional_layers {
    adjust_layer {
        if-layers = <3 2>;
        then-layer = <4>;
    };
};
```

Layer 4（Adjust）はBT管理、レイヤートグル、システム制御を提供します。

### Comboレイヤー（Sticky Key 修飾キー）

修飾キーと編集系コンボは専用の**Comboレイヤー**（レイヤー7）に隔離し、通常タイピング中の誤発動を根本的に防止しています。ComboレイヤーはF長押しで有効になり、全キーが `&none` に設定されているため定義した修飾キーとコンボのみ機能します。

**左手** — Comboレイヤー上のキーマップ直接割当（Fのhold-tapとコンボが競合するためコンボではない）:

| キー | バインディング |
|------|------------|
| A | `&sk LEFT_SHIFT` |
| S | `&sk LCTRL` |
| D | `&sk LEFT_ALT` |

**右手** — レイヤー限定コンボ（`layers = <7>`）:

```dts
cb_kp_r-ctrl {
    bindings = <&sk RCTRL>;
    key-positions = <19 21>;    // J + L
    layers = <7>;               // Comboレイヤーのみ
    require-prior-idle-ms = <150>;
};
```

- **F長押し → 修飾キー → F離す → 次のキー**: 修飾キーが1回だけ適用される
- **スタック**: F長押し → A（Shift）→ S（Ctrl）→ F離す → キー = Ctrl+Shift+キー
- **自動解除**: 次のキー入力後に修飾キーが解除される

### Layer Tap: `lt_g`

`tap-preferred` 方式のlayer-tapビヘイビアです。`require-prior-idle-ms` により高速タイピングでは常に文字を送信し、意図的な長押しのみレイヤーを発動します。**Fキー**（Comboレイヤー）と**Gキー**（Naviレイヤー）の両方で使用しています。

```dts
lt_g: layer_tap_g {
    compatible = "zmk,behavior-hold-tap";
    bindings = <&mo>, <&kp>;
    flavor = "tap-preferred";
    tapping-term-ms = <HM_TAPPING_TERM>;
    quick-tap-ms = <200>;
    require-prior-idle-ms = <HM_PRIOR_IDLE>;
};
```

| キー | タップ | ホールド | レイヤー |
|------|--------|---------|---------|
| F | F | Comboレイヤー（レイヤー7） | 修飾キー・編集系コンボ |
| G | G | Naviレイヤー（レイヤー5） | 矢印キー・マウス・スクロール |

- **高速タイピング中**: 常に文字送信（レイヤー発動なし）

## ファームウェア設定（`cornix.conf`）

| 設定 | 値 | 目的 |
|------|------|------|
| `CONFIG_BT_CTLR_TX_PWR_PLUS_8` | `y` | BT送信電力を上げて分割接続を安定化 |
| `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT` | `900000` | 15分アイドルでスリープ（バッテリー節約） |
| `CONFIG_ZMK_KSCAN_DEBOUNCE_PRESS_MS` | `10` | キー押下のdebounce（チャタリング防止） |
| `CONFIG_ZMK_KSCAN_DEBOUNCE_RELEASE_MS` | `10` | キー解放のdebounce（押しっぱなし防止） |
| `CONFIG_BT_PERIPHERAL_PREF_MIN_INT` | `6` | BT接続間隔の最小値（7.5ms、安定性重視） |
| `CONFIG_BT_PERIPHERAL_PREF_MAX_INT` | `12` | BT接続間隔の最大値（15ms、安定性重視） |
