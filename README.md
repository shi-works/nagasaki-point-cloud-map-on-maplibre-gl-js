# nagasaki-point-cloud-map-on-maplibre-gl-js
## Public Website
https://shi-works.github.io/nagasaki-point-cloud-map-on-maplibre-gl-js/

## 3D Tilesの生成方法
- pdal 2.5.2
- py3dtiles 6.0.0
```
# OSGeo4Wを起動
# PDALとJSONを利用して複数ファイルをマージ
pdal pipeline merge-pipeline.json

# メタデータの確認
pdal info --metadata sasebo_station.las

# 座標参照系の付与
pdal translate -i sasebo_station.las -o sasebo_station_translated.las --writers.las.a_srs="EPSG:6669"

# メタデータの確認
pdal info --metadata sasebo_station_translated.las

# データの先頭に格納されているポイントの位置を確認
pdal info -p 0 sasebo_station_translated.las

# PDALとJSONを利用して軸を入れ替え
pdal pipeline xy_switch_pipeline.json

# データの先頭に格納されているポイントの位置を確認
pdal info -p 0 sasebo_station_swaped.las

# WSLを起動（Windows環境では下記のコマンドはエラーになるため）
# py3dtilesで点群データを3DTilesに変換
py3dtiles convert --srs_in 6669 --srs_out 4978 --out sasebo_station las/sasebo_station_swaped.las
```
merge-pipeline.json
```
{
  "pipeline": [
    {
      "type": "readers.las",
      "filename": "sasebo_station/*.las"
    },
    {
      "type": "writers.las",
      "filename": "sasebo_station.las"
    }
  ]
}
```
xy_switch_pipeline.json
```
[
  {
    "type": "readers.las",
    "filename": "sasebo_station_translated.las",
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
    "filename": "sasebo_station_swaped.las",
    "forward": "header,scale,vlr",
    "offset_x": "auto",
    "offset_y": "auto",
    "offset_z": "auto"
  }
]
```
# 3D Tiles(tileset.json)
## 長崎駅
https://xs489works.xsrv.jp/3dtiles/open_nagasaki/nagasaki_station/tileset.json
## 佐世保駅
https://xs489works.xsrv.jp/3dtiles/open_nagasaki/sasebo_station/tileset.json
## ハウステンボス
https://xs489works.xsrv.jp/3dtiles/open_nagasaki/huistenbosch/tileset.json
