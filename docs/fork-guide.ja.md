# フォークガイド

このリポジトリをフォークして、自分のCornix LP用にキーマップをカスタマイズする方法です。

## クイックスタート

1. GitHubでこのリポジトリを **フォーク**
2. `config/cornix.keymap` を編集してキーマップをカスタマイズ
3. pushするとGitHub Actionsが自動でファームウェアをビルド
4. Actionsタブから `.uf2` ファイルをダウンロード

## キーマップの編集

### 方法1: Keymap Editor（GUI）

最も簡単な方法は [Keymap Editor](https://nickcoutsos.github.io/keymap-editor/) を使うことです。

1. [Keymap Editor](https://nickcoutsos.github.io/keymap-editor/) を開く
2. フォークしたリポジトリへのアクセスを許可
3. フォークを選択
4. ビジュアルにキーを編集してコミット

レイアウト定義（`config/cornix.json`）が含まれているので、Keymap EditorでCornix LPのレイアウトが自動的に表示されます。

> **注意**: Keymap Editorはプリプロセッサマクロ（`#define`, `#include`）のサポートが限定的です。Homerow modsなどの高度な機能は正しく表示されない場合がありますが、ビルドには問題ありません。

### 方法2: `.keymap` を直接編集

任意のテキストエディタで `config/cornix.keymap` を編集します。キーマップの構文は [ZMKドキュメント](https://zmk.dev/docs) を参照してください。

## 主要ファイル

| ファイル | 用途 |
|----------|------|
| `config/cornix.keymap` | キーマップ定義（レイヤー、コンボ、ビヘイビア） |
| `config/cornix.conf` | ファームウェア設定（BT電力、スリープタイムアウト） |
| `config/cornix.json` | Keymap Editor用の物理レイアウト |
| `config/includes/cornix54.h` | コンボ用のキーポジション定義 |
| `config/west.yml` | ZMKとモジュールの依存関係 |
| `build.yaml` | GitHub Actionsビルドマトリクス |

## コンボの追加

コンボは `cornix.keymap` の `combos` セクションで定義します。キーポジション番号は `config/includes/cornix54.h` を参照してください:

```
╭────────────────────────╮               ╭────────────────────────╮
│  0   1   2   3   4   5 │               │  6   7   8   9  10  11 │
│ 12  13  14  15  16  17 ╰──╮         ╭──╯ 18  19  20  21  22  23 │
│ 24  25  26  27  28  29  30╰────┬────╯31  32  33  34  35  36  37 │
│ 38  39  40      41  42  43     │     44  45  46      47  48  49 │
╰────────────────────────────────┴────────────────────────────────╯
```

コンボの例:

```dts
cb_kp_esc {
    bindings = <&kp ESC>;
    key-positions = <2 3>;    // W + E
    layers = <0 1>;           // BaseとWinレイヤーでのみ有効
};
```

## BT・電力設定の変更

`config/cornix.conf` を編集します:

```ini
# BT送信電力を上げて分割接続を安定化
CONFIG_BT_CTLR_TX_PWR_PLUS_8=y

# アイドルスリープまでの時間（ミリ秒、デフォルト: 900000 = 15分）
CONFIG_ZMK_IDLE_SLEEP_TIMEOUT=900000
```

## ビルドに関する注意

このリポジトリはZMKのCIワークフローを特定のコミットにピン留めしています。これはZMKのboard variantチェック（Zephyr 4.1）との[既知の互換性問題](https://github.com/hitsmaxft/zmk-keyboard-cornix/issues/26)を回避するためです。upstreamのCornixボード定義がZMK variant対応したら、ワークフローを `@main` に更新できます。
