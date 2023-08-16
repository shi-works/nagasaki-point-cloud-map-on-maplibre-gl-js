# nagasaki-point-cloud-map-on-maplibre-gl-js
## Public Website
https://shi-works.github.io/nagasaki-point-cloud-map-on-maplibre-gl-js/

## 3DTilesの生成方法
- pdal 2.5.2
- py3dtiles 6.0.0
```
# OSGeo4Wを起動
# メタデータの確認
pdal info --metadata 01ke9821_org.las

# 座標参照系の付与
pdal translate -i 01ke9821_org.las -o 01ke9821_translated.las --writers.las.a_srs="EPSG:6669"

# メタデータの確認
pdal info --metadata 01ke9821_translated.las

# データの先頭に格納されているポイントの位置を確認
pdal info -p 0 01ke9821_translated.las

# PDALとJSONを利用して軸を入れ替え
pdal pipeline xy_switch_pipeline.json

# データの先頭に格納されているポイントの位置を確認
pdal info -p 0 01ke9821_swaped.las

# WSLを起動（Windows環境では下記のコマンドはエラーになるため）
# py3dtilesで点群データを3DTilesに変換
py3dtiles convert --srs_in 6669 --srs_out 4978 --out 01KE9821 01ke9821_swaped.las
```

```
xy_switch_pipeline.json
```json
[
  {
    "type": "readers.las",
    "filename": "01ke9821_translated.las",
    "spatialreference": "EPSG:6669"
  },
  {
    "type": "filters.reprojection",
    "in_srs": "EPSG:6669",
    "out_srs": "EPSG:6669",
    "in_axis_ordering": "2, 1"
  },
  {
    "type": "writers.las",
    "filename": "01ke9821_swaped.las",
    "forward": "header,scale,vlr",
    "offset_x": "auto",
    "offset_y": "auto",
    "offset_z": "auto"
  }
]

```
