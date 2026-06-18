# keymap-drawer 再生成手順

`README.md` / `README.ja.md` が参照する `docs/keymap.svg` を最新の `config/cornix.keymap`
から再生成するための設定一式。

## ファイル

- `cornix_info.json` — cornix54 の物理レイアウト (QMK info.json 形式)。
  既存 SVG のキー座標・親指回転 (±11.93° / ±23.0°) から復元したもの。1u = 56px。
- `config.yaml` — 描画設定 (key_w/key_h=56 等、既存 SVG のジオメトリに一致)。

## 再生成

keymap-drawer は隔離 venv に導入する (システムを汚さない)。

```sh
python3 -m venv ~/.cache/zmk-keymap-drawer-venv
~/.cache/zmk-keymap-drawer-venv/bin/pip install keymap-drawer

# parse → draw (parse にも -c を渡す: config.yaml の parse_config.raw_binding_map を効かせ
#   &leader 等の生バインディングを読みやすい凡例に変換するため)
VENV=~/.cache/zmk-keymap-drawer-venv
"$VENV/bin/keymap" -c keymap_drawer/config.yaml \
    parse -z config/cornix.keymap > /tmp/cornix.parsed.yaml
"$VENV/bin/keymap" -c keymap_drawer/config.yaml \
    draw -j keymap_drawer/cornix_info.json -l LAYOUT \
    /tmp/cornix.parsed.yaml > docs/keymap.svg
```

中間生成物 (`*.parsed.yaml`) はコミットしない。`config/cornix.keymap` が唯一の真実源。

## 注意

- 物理レイアウトを変えた場合 (キー追加/移動) のみ `cornix_info.json` を更新する。
  キーマップの凡例変更だけなら再 parse → draw で追従する。
- 親指キーの回転は QMK `r`(度) + `rx`/`ry`(中心) で表現。SVG の `rotate()` と符号一致を確認済み。
