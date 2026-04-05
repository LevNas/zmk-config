# zmk-config

[Cornix LP](https://github.com/hitsmaxft/zmk-keyboard-cornix) 分割キーボード（ロータリーエンコーダー付き）のZMKファームウェア設定です。

[English](README.md)

## キーマップ

![Cornix LP Keymap](docs/keymap.svg)

コンボキーの一覧は [コンボリファレンス](docs/combos.ja.md)、ビヘイビア・マクロ・ファームウェア設定の詳細は [設定リファレンス](docs/configuration.ja.md) を参照してください。

## ビルド

GitHubにpushすると自動でGitHub Actionsがビルドを実行します。
[最新のActionsラン](https://github.com/LevNas/zmk-config/actions) から `.uf2` ファイルをダウンロードできます。

### フラッシュ方法

1. キーボード半分をUF2ブートローダーモードにする（リセットボタンをダブルタップ）
2. 左側に `cornix_left.uf2`、右側に `cornix_right.uf2` をコピー
3. 別のファームウェアから切り替える場合は、先に両側に `reset.uf2` をフラッシュしてBTボンディング情報をクリア

## カスタマイズ

自分のキーマップを作りたい場合は、GitHub で **「Use this template」** をクリックしてリポジトリを作成してください。詳しくは [セットアップガイド](docs/fork-guide.ja.md) を参照。

## ハードウェア

- ボード: Cornix LP（分割、nRF52840）
- ロータリーエンコーダー: 2個（左: 音量、右: Page Up/Down）
- ドングルなし

## 謝辞

- [hitsmaxft](https://github.com/hitsmaxft) — [Cornix LP](https://github.com/hitsmaxft/zmk-keyboard-cornix) のボード設計とZMKボード定義
- [urob](https://github.com/urob) — [zmk-helpers](https://github.com/urob/zmk-helpers) と Homerow mods のリファレンス設定
- [ZMK Contributors](https://zmk.dev/) — ZMK ファームウェア
