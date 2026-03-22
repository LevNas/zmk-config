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

### Gキー専用Layer Tap: `lt_g`

Gキー用の特別なlayer-tapビヘイビアです。`tap-preferred` と `require-prior-idle-ms` を使い、高速タイピング（「が」「ぎ」「gg」等）では常に文字を送信し、意図的な長押しのみNaviレイヤーを発動します。

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

- **タップ**: G
- **ホールド**（250ms以上、アイドル後）: Naviレイヤー（レイヤー5）
- **高速タイピング中**: 常にG送信（レイヤー発動なし）

## ファームウェア設定（`cornix.conf`）

| 設定 | 値 | 目的 |
|------|------|------|
| `CONFIG_BT_CTLR_TX_PWR_PLUS_8` | `y` | BT送信電力を上げて分割接続を安定化 |
| `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT` | `900000` | 15分アイドルでスリープ（バッテリー節約） |
| `CONFIG_ZMK_KSCAN_DEBOUNCE_PRESS_MS` | `5` | キー押下のdebounce（チャタリング防止） |
| `CONFIG_ZMK_KSCAN_DEBOUNCE_RELEASE_MS` | `5` | キー解放のdebounce（押しっぱなし防止） |
| `CONFIG_BT_PERIPHERAL_PREF_MIN_INT` | `6` | BT接続間隔の最小値（7.5ms、安定性重視） |
| `CONFIG_BT_PERIPHERAL_PREF_MAX_INT` | `12` | BT接続間隔の最大値（15ms、安定性重視） |
