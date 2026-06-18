# キーマップ設計判断ログ

`config/cornix.keymap` の構成と、その背後にある設計判断の記録。

## 設計目標 (現行)

1. **打鍵リズムを乱さない** — urob 流 timeless HRM の `require-prior-idle-ms` ゲートで、速打中は修飾キーを一切出さない (「ブレーキ感」ゼロ)
2. **修飾キーは 1 キーで取れる** — home 行ホールドで Shift / Ctrl / Alt / GUI、左右両手から
3. **外周列に依存しない** — 小指外側・親指外側を捨てた 36-key 有効レイアウト
4. **エンコーダはレイヤーごとに振る舞いを変える**
5. **ZMK Studio でライブ編集**できるようにする (build variant として組み込み)
6. **低頻度コマンドはリーダーキーの namespace に逃がす** (IDE ジェスチャ等)

## 設計の歴史と現在地

### Phase 1: filterpaper 流 HM 採用 (一時的、廃止)

最初の試みは [filterpaper/zmk-config](https://github.com/filterpaper/zmk-config) を参考に「H は素キー、JKL/SQT は HM」という非対称 HM 配置を採用：

- `tapping-term-ms = 225` / `require-prior-idle-ms = 105` (filterpaper 流)
- `hm_shift_l/r` は `require-prior-idle-ms = 0` で即時 Shift 化 (Townk 流)
- `hold-trigger-on-release` で同手 mod 併用を許可

**結果**: 打鍵リズム不適合により**左右両手で頻発する誤発火**が発生。リモート操作中に Win / Alt 系ショートカットが暴発し Wi-Fi 設定 OFF からのリモート切断事故に至る。`require-prior-idle-ms` を緩めても根本解決せず。

### Phase 2: HM 全撤廃 (廃止 → Phase 5 で撤回)

`A/S/D/F` と `J/K/L/;` を素キーへ戻し、修飾キーへのアクセスをすべて再設計。

> **訂正 (Phase 5)**: この時点の診断「urob 流でもブレーキ感は解決しない」は**誤り**だった。真因は filterpaper/Townk 混成設定で Shift 用 hold-tap だけ `require-prior-idle-ms = 0` だったこと (速打時に Shift が暴発しテンポを乱す)。urob 流のように **全 mod を一律 150 ms に統一**すれば速打中の暴発は起きない。詳細は Phase 5 を参照。

当時の調査で判明した事実：

- ZMK コミュニティの主流派は **urob 流 timeless HM** (`tapping-term 280ms` 等、より緩い設定) を採用している。当時は本質的な機構 (`hold-trigger-on-release` + `balanced` flavor) が filterpaper と同じなので解決にならないと判断したが、後に `require-prior-idle-ms` の一律化が肝だったと判明した (Phase 5)
- HM 撤廃派は 3 系統:
  - **Callum 流** (sticky-on-layer): [dxmh/zmk-config](https://github.com/dxmh/zmk-config), [braindefender/wellum](https://github.com/braindefender/wellum)
  - **Combo 置換**: [alex-tdrn/zmk-config](https://github.com/alex-tdrn/zmk-config), [casuanoob/zmk-config-bkb](https://github.com/casuanoob/zmk-config-bkb)
  - **anachron 流** (combo 多用 + thumb sticky shift + sticky-on-layer): [theol0403/anachron-zmk-config](https://github.com/theol0403/anachron-zmk-config)

### Phase 3: 完全コンボ主義 (廃止 → Phase 5 で HRM へ戻す)

調査結果と本人の打鍵スタイルから「**修飾キーを完全に combo に集約する**」方針へ確定 (後に Phase 5 で HRM 一本化へ撤回)。

設計上のポイント：

- 修飾コンボの bindings は **`&kp LXXX` (直接キープレス)** を使用 (`&sk` ではなく)
  - 押し続けで修飾保持 (連続キー操作対応)
  - 素早く 2 回タップで IDE の Ctrl-Ctrl 等のダブルタップ検出
  - 複数コンボ同時保持で多重修飾の sustained ホールド
- **mod-morph による DEL アクセス**: Backspace コンボ (`bs_del`) に Shift コンボを重ねると Delete が発射される。Delete 専用コンボは設けない

### Phase 4: G→Navi 復活 + 括弧の Symbols 集約 + J+; Enter (2026-05-29)

実機検証を経て、以下の調整を実施:

- **G キーの layer-tap 復活**: `lt_g 4 G` (tap=G / hold=Navi) を main ブランチ準拠の `tapping-term=250 / require-prior-idle-ms=70` で再導入。HM 撤廃後も唯一「操作感が良い」とユーザが評価した hold-tap で、Navi レイヤーへの主要な入口を担う
- **括弧の Symbols レイヤー集約**: `< > [ ] { } ( )` を Symbols レイヤーへ移動。これまで base 層の **vertical pair combo** (T+G で `{` 等) で取得していたが、垂直チョーディングの打鍵感が悪く撤去。Symbols 上段に `< > [ ] { }` の 6 個を新規配置 (`( )` は既に home 行末尾にあり)
- **J + ; → Enter コンボ追加**: Base 層に右手 home 行末端のコンボとして Enter を追加。既存の右親指 Enter (位置 44) に加えて home position から手を離さずに Enter が打てる代替経路
- **Symbols 下段の旧 Shift キー除去**: 旧 LEFT_SHIFT (位置 25) / RIGHT_SHIFT (位置 37) は combo Shift で代替されたため `&none` 化

### Phase 5: timeless HRM 再導入 + リーダーキー (現行 2026-06)

Phase 2-3 の「HM 全撤廃 → 完全コンボ主義」を**撤回**し、urob 流 timeless HRM を home 行に再導入した。

**撤回の根拠 (Phase 2 診断の訂正)**:

- 2026-05 に HM を撤去した真因は、filterpaper/Townk 混成設定で **Shift 用 hold-tap だけ `require-prior-idle-ms = 0`** だったこと。これが速打時に Shift を暴発させテンポを乱していた。HM 機構そのものの欠陥ではなかった
- urob 流のように **全 mod (Shift 含む) を一律 `require-prior-idle-ms = 150`** に統一すると、直前打鍵から 150 ms 以内のキーは長押ししても即タップに倒れる。速い連打/ロール中は mod が一切出ずレイテンシゼロで、暴発が解消した

**採用した HRM 設定** (`hm_l` / `hm_r`):

- `flavor = "balanced"` / `tapping-term-ms = 280` / `require-prior-idle-ms = 150` (全 mod 一律。Shift も同値) / `quick-tap-ms = 175`
- `hold-trigger-on-release` + `hold-trigger-key-positions` を反対手 + 親指のみに限定 (positional)
- これで「速打のテンポを乱さず 1 キーで修飾も取れる」を両立

**home 行 mod 配列** (urob 純正、小指 → 人差し指):

| | 左 | | 右 |
|--|----|--|----|
| 小指 | A = LGUI | 人差し指 | J = LCTRL |
| 薬指 | S = LALT | 中指 | K = LSHFT |
| 中指 | D = LSHFT | 薬指 | L = LALT |
| 人差し指 | F = LCTRL | 小指 | ; = LGUI |

`G` は `lt_g 4` (ホールドで Navi、Phase 4 から継続)、`H` は素のキー。

**修飾コンボ 8 個を撤去**: 旧 `cb_lshft` / `cb_lctrl` / `cb_lalt` / `cb_lgui` / `cb_rshft` / `cb_rctrl` / `cb_ralt` / `cb_rgui` を削除し、修飾アクセスを HRM に一本化。多重修飾 (Ctrl+Alt 等) は複数 HRM の同時ホールドで従来どおり可能。

**zenhan combo / マクロを撤去**: 旧 E+R に割り当てていた全角/半角トグル (Alt+`) の combo と `my_macro_zenhan` マクロを削除 (未使用のため)。

**リーダーキーを新規導入**: urob の `zmk-leader-key` モジュールを `config/west.yml` に追加 (ZMK 公式 `main` 未収録のためモジュール提供)。

- 開始キー: combo **E + I** (位置 2+9、左右中指のクロスハンド、`require-prior-idle-ms = 150`)。※ Phase 6 で開始キーは **D + F** へ変更し、`df_lead` mod-morph で Alt 保持中は Alt+Tab になった (下記 Phase 6 参照)
- このモジュール版は **timeout / layers / immediate-trigger を持たない**モーダル動作。有効な並びが揃うまで待機し、無効キーで中断する
- 現行シーケンス:
  - `leader → c c`: Ctrl 二連打 (`ctrl_ctrl`、例: IDE の Run Anything)
  - `leader → s s`: Shift 二連打 (`shift_shift`、例: IDE の Search Everywhere)
- 二連打マクロは `wait-ms = tap-ms = 40`。host が double-tap と認識する間隔は IDE 依存で要調整
- **使い分けの定石**: 高頻度の単発操作は leader (最低 2 打鍵) より combo 直結が速い。leader は低頻度・多数・ニーモニックな namespace 向き

### Phase 6: combo 大幅拡張 + 誤爆対策チューニング (現行 2026-06)

Phase 5 の少数コンボから、記号・括弧・編集系を含む大規模なコンボ集合へ拡張し、文字キー直上に乗るコンボの誤爆を抑える新しいチューニング方針を確立した。

**既存コンボの変更・撤去**:

- **親指 Esc の撤去**: `&lt 5 ESCAPE` → 素の `&mo 5`。親指タップでは Esc が出なくなり、Esc は **W + E コンボのみ**に。同じ親指の**ホールド**は従来どおり NumPad (layer 5) を起動 (ホールド挙動は不変)
- **Tab**: S + F → **S + D**
- **Leader 開始キー**: E + I → **D + F**。さらに `df_lead` **mod-morph 化**: 修飾なしは leader、**Alt 保持中は Tab** (`keep-mods` で Alt を残し Alt+Tab。Alt を保持したまま D + F 連打でウィンドウ循環)
- **Delete 専用コンボ U + I を新規追加** (`&kp DEL`)。従来の「Shift + Backspace コンボ → DEL」(mod-morph) も併存
- **`k;` (K + ;) アポストロフィコンボを撤去** → `'` は新規 **J + L** コンボへ移行
- **G + B の Space コンボを撤去** → G + B は **`~`** に転用。Space は親指 `lt 4 SPACE` に残存
- **Enter (J + ;) は据置**だが最もスパンが広いため timeout を最も緩く (`75 ms`、`COMBO_TERM_WIDE`) に

**新規コンボ群** (いずれも base 層、縦ペアまたは隣接ペア):

- 記号 (素 `&kp`): `@ # $ % ^ + * &` と `` ` `` `\ = ~ _ - / |` の 16 個
- 括弧・引用符 (Shift 連動 mod-morph: `cm_quote` / `cm_lpar` / `cm_rpar` / `cm_lbkt` / `cm_rbkt`): `'`(J+L) `(`(J+K) `)`(K+L) `[`(M+,) `]`(,+.)。Shift で `" < > { }` に変化
- Ins/Del 派生: Ctrl+Insert (X+C)、Shift+Insert (C+V)、Shift+Delete (X+V)

**誤爆対策チューニング方針 (新方針)**:

- `require-prior-idle-ms = 175` (`COMBO_IDLE`) は全コンボ一律で据置。`timeout-ms` は指のジオメトリで **三段階**化:
  - **同指縦コンボ = `COMBO_TERM = 30`** (記号 16 個)。1 本指で上下 2 キーを叩くため指間ズレが無く短い窓で成立。かつ同指縦の連続入力は通常しないため誤発火 (FP) にも構造的に強い
  - **別指横並びコンボ = `COMBO_TERM_HORIZ = 60`** (Delete U+I、Backspace I+O、Insert O+J、`'` J+L、`(` J+K、`)` K+L、`[` M+,、`]` ,+.)。別々の指で押すとズレ (長い中指が先行・小指が遅れる) が出て `30` では揃わず**不発 (FN)** になったため緩めた
  - **index+小指の Enter (J + ;) = `COMBO_TERM_WIDE = 75`**。最もスパンが広く小指が遅れるため最も緩い
- **設計意図 (FN/FP の別軸整理)**: **不発 (FN) は `timeout-ms` を上げて**対処、**誤発火 (FP) は `require-prior-idle-ms` で抑える**。idle ゲートが打鍵流中 (直前 175 ms 以内) のコンボを無効化するため、`timeout-ms` を緩めても "io" / "ui" 等の bigram 誤発火は基本出ない
- 右手ホーム **J / K / L** はコンボが最も密集 (`'` `(` `)` + `+ * &` 記号列 + J + ; Enter) し、**最大の誤爆リスク帯**。チューニング時はまずここを見る
- これら三段階は**実機チューニングで決めた現時点の暫定値**で、長期使用での FP 観察に応じて調整しうる

## 物理レイアウト前提

Cornix LP (54 キーポジション + 左右ロータリーエンコーダ 2 個)。実機にはすべてのキーが存在するが、論理的には外周を `&none` に落として 36-key 相当として運用する。

`&none` 化したポジション:

| 位置 | 元の役割 | 代替手段 |
|------|---------|---------|
| 0    | ESC     | combo (1+2 = W+E) |
| 11   | `\`     | combo (14+26 = D+C)、または Symbols レイヤー |
| 12   | Tab + Ctrl HM | Tab combo (13+14 = S+D)、Ctrl は home 行 HRM (F = Ctrl) |
| 23   | `'`     | combo (20+22 = J+L) |
| 24   | LSHIFT  | home 行 HRM (D = Shift) |
| 37   | RSHIFT  | home 行 HRM (K = Shift) |
| 38-40, 47-49 | 外側親指 | (元から `&none`) |

## ホームロウ Mods 配置 (Phase 5)

修飾キーは home 行のホームロウ Mods (`hm_l` / `hm_r`) で取得する。urob 純正配列 (小指 → 人差し指):

### 左手

| 修飾 | キー | 位置 | 指 |
|------|------|------|-----|
| GUI   | A | 12 | 小指 |
| Alt   | S | 13 | 薬指 |
| Shift | D | 14 | 中指 |
| Ctrl  | F | 15 | 人差し指 |

### 右手

| 修飾 | キー | 位置 | 指 |
|------|------|------|-----|
| Ctrl  | J | 20 | 人差し指 |
| Shift | K | 21 | 中指 |
| Alt   | L | 22 | 薬指 |
| GUI   | ; | 23 | 小指 |

- **多重修飾**: 複数 HRM の同時ホールドで実現 (同手の F+D = Ctrl+Shift、対角の 左F + 右L = Ctrl+Alt 等)
- **positional**: `hold-trigger-key-positions` を反対手 + 親指に限定。home 行キーをホールドしたまま同手の文字を打っても文字が出る (Vim の hjkl 等と衝突しない)
- `G` は `lt_g 4` (ホールドで Navi)、`H` は素のキー

## コンボ一覧 (Phase 6)

全コンボ共通: `require-prior-idle-ms = 175`。`timeout-ms` は三段階 (同指縦 `30` / 別指横 `60` / Enter J+; `75`)。完全な一覧は [コンボリファレンス](combos.ja.md) を正とする。

### 編集 / ナビ

| Combo  | 位置 | 動作 | 備考 |
|--------|------|------|------|
| Esc    | W + E (1+2) | `&kp ESC` | コンボのみ (親指は `&mo 5` に変更) |
| Tab    | S + D (13+14) | `&kp TAB` | |
| Delete | U + I (8+9) | `&kp DEL` | 専用 Delete (新規) |
| Backspace | I + O (9+10) | `&bs_del` | Shift (D/K HRM) + これで Delete |
| Insert | O + J (10+20) | `&kp INS` | クロスハンド |
| Enter  | J + ; (20+23) | `&kp ENTER` | timeout 最緩 (75)。右親指 (位置 44) でも取得可 |
| Leader | D + F (14+15) | `&df_lead` | 通常は leader、Alt 保持中は Tab (Alt+Tab) |

### 記号 (素 `&kp`)

`@`=W+S `#`=E+D `$`=R+F `%`=T+G `^`=Y+H `+`=U+J `*`=I+K `&`=O+L
`` ` ``=S+X `\`=D+C `=`=F+V `~`=G+B `_`=H+N `-`=J+M `/`=K+, `|`=L+.

### 括弧・引用符 (Shift で別記号、mod-morph)

`'`=J+L (Shift `"`) / `(`=J+K (Shift `<`) / `)`=K+L (Shift `>`) / `[`=M+, (Shift `{`) / `]`=,+. (Shift `}`)

### Ins/Del 派生

Ctrl+Insert=X+C / Shift+Insert=C+V / Shift+Delete=X+V

## レイヤー構成 (7 レイヤー)

| # | 名前 | 入口 | 用途 |
|---|------|------|------|
| 0 | Base | デフォルト | 文字入力 |
| 1 | Symbols (LOWER) | 親指 `mo 1` (位置 42) | 記号・数字・F-keys |
| 2 | Mix (RAISE) | 親指 `mo 2` (位置 45) | F-keys、数字行、編集系 |
| 3 | Adjust | conditional (1+2 同時押し) | BT / 電源 |
| 4 | Navi | 親指 `lt 4 SPACE` ホールド (位置 43) | カーソル移動 |
| 5 | NumPad | 親指 `mo 5` ホールド (位置 41) | テンキー (Phase 6 で `lt 5 ESCAPE` → `mo 5`) |
| 6 | Debug | Navi/NumPad 内の `mo 6` | デバッグ |

**廃止: 旧 Combo レイヤー (旧 index 6)** — sticky modifier combos は HM (今は撤廃) の代替として用意されていたが、現行の修飾コンボ設計で不要となった。Debug は 7 → 6 に番号を繰り上げ。

## エンコーダのレイヤー別マッピング

ZMK の `sensor-bindings` はレイヤー未定義時に下位レイヤーへフォールバックする。レイヤーごとに必要な操作だけ上書き。

| レイヤー | 左つまみ | 右つまみ |
|---------|---------|---------|
| Base    | 音量 (Vol Up/Dn) | ページ (PgUp/PgDn) |
| Symbols | カーソル左右 | カーソル上下 |
| Mix     | ブラウザタブ (Ctrl+Tab) | 音量 |
| Navi    | 行頭/行末 (End/Home) | ページ (PgDn/PgUp) |
| NumPad  | +/- | ×/÷ |
| Adjust / Debug | (Base へフォールバック) | (Base へフォールバック) |

## ZMK Studio との運用

`config/cornix.conf` で `CONFIG_ZMK_STUDIO=y` + `CONFIG_ZMK_STUDIO_LOCKING=n` を設定し、`build.yaml` の cornix_left に `studio-rpc-usb-uart` snippet を追加して **Central (左半身) のみ**で Studio 機能を有効化。

注意点：

- **Studio で編集できないもの**: `behaviors` ブロック (mod-morph / レイヤータップ / HM 定義など)、`conditional_layers`、`macros`、`sensor-bindings`、`.conf` 設定
- **Studio で編集できるもの**: 各レイヤーのキー割当、レイヤー名、一部のコンボ
- **Studio 編集の落とし穴**: Studio 編集後にコード側を変更してファーム焼き直しすると、Studio で詰めた配置が古いコード版で上書きされる。**コード側を Single Source of Truth** として運用するのが安全

## 参考事例

設計判断の参考になった ZMK 公開設定：

- [theol0403/anachron-zmk-config](https://github.com/theol0403/anachron-zmk-config) — HM 撤廃 + ~100 combo + mod-morph の集大成
- [dxmh/zmk-config](https://github.com/dxmh/zmk-config) — Callum 流 sticky-on-layer の参照実装
- [braindefender/wellum](https://github.com/braindefender/wellum) — Callum を ZMK に素直に移植
- [alex-tdrn/zmk-config](https://github.com/alex-tdrn/zmk-config) — 「層を減らす」combo 派の代表
- [SethMilliken/zmk-config](https://github.com/SethMilliken/zmk-config) — 30+ combo の物理配置設計
- [urob/zmk-config](https://github.com/urob/zmk-config) — **timeless HM (Phase 5 で採用)** + helpers ライブラリ。`zmk-leader-key` モジュールも本構成で採用
- [filterpaper/zmk-config](https://github.com/filterpaper/zmk-config) — Phase 1 で参考にした非対称 HM (実装は撤廃。Shift の prior-idle=0 が後の暴発の真因)

## 撤退した設計判断 (記録)

- **filterpaper/Townk 混成 HM (Phase 1)**: Shift 用 hold-tap だけ `require-prior-idle-ms = 0` で速打時に Shift が暴発。これが真因だったと Phase 5 で再診断
- **完全コンボ主義による修飾キー (Phase 2-3)**: 修飾コンボ 8 個を撤去し、timeless HRM へ一本化 (Phase 5)。home 行 1 キーで修飾が取れるほうが速い・覚えやすいと判断
- **zenhan combo / `my_macro_zenhan` (E+R, Alt+`)**: 未使用のため撤去 (Phase 5)。再度全/半切替が必要になれば Alt+` の tap マクロを復活させる
- **Combo レイヤー (旧 index 6)**: sticky modifier combo の専用入口だったが不要となり廃止
- **Shift 専用親指キー (anachron 流)**: 親指の役割が既に飽和しているため不採用
- **`k;` (K + ;) アポストロフィコンボ (Phase 6 で撤去)**: `'` を J + L へ移行。`'` を右手 home 行内側に集約するため
- **G + B の Space コンボ (Phase 6 で撤去)**: G + B を `~` に転用。Space は親指 `lt 4 SPACE` に残るため重複が不要
- **親指 Esc (`lt 5 ESCAPE` のタップ側、Phase 6 で撤去)**: 親指を `&mo 5` (ホールド専用) に変更。Esc は W + E コンボのみに。親指タップの Esc 暴発を防ぎ NumPad ホールドだけ残す
