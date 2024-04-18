# VRMC_springBone_extended_collider-1.0

*Version 1.0-draft*

## Contributors

- 0b5vr

## Status

Complete

## Dependencies

glTF 2.0仕様に向けて策定されています。

本仕様は [`VRMC_springBone`](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_springBone-1.0/README.ja.md) に依存します。

## Overview

`VRMC_springBone_extended_collider` 仕様は、 `VRMC_springBone` で定義されたコライダーの形状を追加するglTF拡張です。
本拡張は、球の内部コライダー・カプセルの内部コライダー・平面コライダーを定義します。

拡張コライダーは、 `VRMC_springBone` で定義されたコライダーに対して、 `VRMC_springBone_extended_collider` 拡張を追加することで定義します。

TBD

`VRMC_springBone_extended_collider` 拡張でコライダーが定義されている場合、 `VRM_springBone` で定義されたコライダーは無視しなければなりません。

> Implenentation Note: `plane` コライダーを定義する際、 `VRMC_springBone_extended_collider` に対応しない環境においてフォールバックのコライダーでも平面コライダーに近い挙動を実現できるよう、半径を十分に大きくした球コライダーとするなどの回避策を検討してください。

> Implementation Note: 内部コライダーとなる球・カプセルコライダーを定義する際、 `VRMC_springBone_extended_collider` に対応しない環境においてフォールバックのコライダーが影響を及ぼさないよう、位置を原点から遠く離した球コライダーとするなどの回避策を検討してください。

## glTF Schema Updates

### Extending Nodes

コンストレイントは、 `VRMC_springBone` で定義されたコライダーに `VRMC_springBone_extended_collider` 拡張を追加することで記述されます。

```json
{
  "extensionsUsed": {
    "VRMC_springBone",
    "VRMC_springBone_extended_collider"
  },
  "extensions": {
    "VRMC_springBone": {
      "specVersion": "1.0",
      "colliders": [
        {
          "node": 0,
          "shape": {
            "sphere": {
              "radius": 0.0,
              "offset": [0.0, -10000.0, 0.0]
            }
          },
          "extensions": {
            "VRMC_springBone_extended_collider": {
              "specVersion": "1.0-draft",
              "shape": {
                "sphere": {
                  "radius": 0.5,
                  "offset": [0.0, 0.0, 0.0],
                  "inside": true
                }
              }
            }
          }
        },
        // ...
      ]
    }
  },
  // 通常のglTF 2.0の情報
  "nodes": [
    // ...
  ]
}
```
