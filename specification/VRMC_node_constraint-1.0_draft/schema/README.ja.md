# VRMC_node_constraint

## Contributors

* 進藤哲郎
* 蘇柏彰
* 小渕豊

## Status

Draft

## Dependencies

Written against the glTF 2.0 spec.

## Overview

```json
{
  "extensionsUsed": {
    "VRMC_node_constraint"
  },
  "extensions": {
    "VRMC_node_constraint": {
      // constraint の配列
      "constraints": [
        {
            // Constraint
        }
      ]
    }
  }
  // 通常のGLTF-2.0の情報
  "materials": [
    "extensions": {
      "VRMC_materials_mtoon": {}
    }
  ],
  "nodes": [
    "extensions": {
      "VRMC_node_collider": {}
    }
  ]
}
```

### `VRMC_Constraints.constraints[*]` Constraint の情報

以下の３つが排他で格納できます。

| 名前     | 備考                             |
|:---------|:---------------------------------|
| position | 他のノードの移動をコピーする設定 |
| rotation | 他のノードの回転をコピーする設定 |
| aim      | 他のノードの方向を向く設定       |

#### space 座標系

| 名前  | 備考          |
|:------|:--------------|
| model | modelローカル |
| local | nodeローカル  |

#### position

| 名前             | 備考                              |
|:-----------------|:----------------------------------|
| source           | コピー元                          |
| sourceSpace      | コピー元の参照座標系              |
| destinationSpace | コピー先の参照座標系              |
| freezeAxis       | 移動値の指定された軸の値を0にする |
| weight           | 値に乗算する                      |

#### rotation

| 名前             | 備考                                              |
|:-----------------|:--------------------------------------------------|
| source           | コピー元                                          |
| sourceSpace      | コピー元の参照座標系                              |
| destinationSpace | コピー先の参照座標系                              |
| freezeAxis       | 回転値をオイラー角にして指定された軸の値を0にする |
| weight           | 値に乗算する                                      |

#### aim

Yaw, Pitch 角を算出する

| 名前             | 備考                                   |
|:-----------------|:---------------------------------------|
| source           | コピー元                               |
| sourceSpace      | コピー元の参照座標系                   |
| destinationSpace | コピー先の参照座標系                   |
| aimVector        |                                        |
| upVector         |                                        |
| freezeAxis       | Yaw, Pitch お指定された軸の値を0にする |
| weight           | 値に乗算する                           |
