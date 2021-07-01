# VRMC_springBone

*Version 1.0-draft*

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [Overview](#overview)
  - [`VRMC_springBone.specVersion`](#vrmc_springbonespecversion)
  - [`VRMC_springBone.colliders`](#vrmc_springbonecolliders)
  - [`VRMC_springBone.colliderGroups`](#vrmc_springbonecollidergroups)
  - [`VRMC_springBone.springs`](#vrmc_springbonesprings)
    - [スプリングの構成について](#%E3%82%B9%E3%83%97%E3%83%AA%E3%83%B3%E3%82%B0%E3%81%AE%E6%A7%8B%E6%88%90%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)
    - [joints](#joints)
  - [`VRMC_springBone.springs[*].joints[*]`](#vrmc_springbonespringsjoints)
- [SpringBoneのアルゴリズム](#springbone%E3%81%AE%E3%82%A2%E3%83%AB%E3%82%B4%E3%83%AA%E3%82%BA%E3%83%A0)
  - [慣性計算](#%E6%85%A3%E6%80%A7%E8%A8%88%E7%AE%97)
  - [衝突判定](#%E8%A1%9D%E7%AA%81%E5%88%A4%E5%AE%9A)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

* 進藤哲郎
* 蘇柏彰
* 小渕豊

## Status

Draft

## Dependencies

Written against the glTF 2.0 spec.

## Overview

簡易な物理風のアニメーションシステム。
髪や衣装が揺れるような見た目に対する用途を想定している。
設定された一連のノード (SpringBone) に慣性アニメーションを実装するものです。
剛性、減速、重力等のパラメーターで挙動を調整できます。
また、SpringBone の各節の末端(球) と、 当たり判定ノード(collider: 球・カプセル)の衝突を設定できます。

```json
{
    "extensionsUsed": [
        "VRMC_springBone"
    ],
    "extensions": {
        "VRMC_springBone": {
            // collider の配列
            "specVersion": "1.0-draft",
            "colliders": [
                {
                    "node": 2,
                    "shape": {
                        "sphere": {
                            "offset": [0, 0, 0],
                            "radius": 1
                        }
                    },
                },
                {
                    "node": 2,
                    "shape": {
                        "capsule": {
                            "offset": [0, 0, 0],
                            "radius": 1,
                            "tail": [
                                0,
                                0,
                                1
                            ]
                        }
                    }
                }
            ],
            // colliderGroup の配列
            "colliderGroups": [
                {
                    // group0
                    "name": "group0",
                    "colliders": [0, 1]
                },
            ],
            // springBone の配列
            "springs": [
                {
                    "joints": [
                        {
                            "node": 0 // node0
                        },
                        {
                            "node": 1 // node1
                        }
                    ],
                    "colliderGroups": [
                        0,
                    ]
                }
            ]
        }
    },
    // 通常のGLTF-2.0の情報
    "nodes": [
        {
            "name": "node0",
        },
        // 省略
    ]
}
```

### `VRMC_springBone.specVersion`

VRMC_springBone 拡張の仕様バージョンを表します。

```json
extensions.VRMC_springBone.specVersion = "1.0-draft"
```

### `VRMC_springBone.colliders`

SpringBone に対する当たり判定を定義します。
対象のノードとその形状 (shape) です。

```json
{
    "extensions": {
        "VRMC_springBone": {
            "colliders": [
                {
                    "node": 1,
                    "shape": {
                        "sphere": {
                            "offset": [0, 0, 0],
                            "radius": 1
                        }
                    },
                },
                {
                    "node": 1,
                    "shape": {
                        "offset": [0, 0, 0],
                        "radius": 1,
                        "tail": [0, 0, 1]
                    },
                }
            ]
        }
    }
}            
```

shape は `sphere` または `capsule` のどちらかで排他です。

| key                  | type    | 備考                                                                               |
|:---------------------|:--------|:-----------------------------------------------------------------------------------|
| node                 | integer | 対象のノード                                                                       |
| shape.sphere.offset  | float3  | shapeが球の場合のみ:対象ノードのローカル座標での球の中心位置                       |
| shape.sphere.radius  | float   | shapeが球の場合のみ:球の半径                                                       |
| shape.capsule.offset | float3  | shapeがカプセルの場合のみ:対象ノードのローカル座標でのカプセル始点側半円の中心位置 |
| shape.capsule.radius | float   | shapeがカプセルの場合のみ:カプセルの半円部と円柱部の半径                           |
| shape.capsule.tail   | float3  | shapeがカプセルの場合のみ:対象ノードのローカル座標でのカプセル終点側半円の中心位置 |

### `VRMC_springBone.colliderGroups`

```json
{
    "extensions": {
        "VRMC_springBone": {
            "colliderGroups": [
                {
                    "name": "groupName",
                    "colliders": [0, 1, 2]
                }
            ]
        }
    }
}
```

| key       | type      | 備考                                                     |
|:----------|:----------|:---------------------------------------------------------|
| name      | string    | グループの名前                                           |
| colliders | integer[] | 前項の VRMC_springBone.colliders に対する index のリスト |

### `VRMC_springBone.springs`

```json
{
    "extensions": {
        "VRMC_springBone": {
            "springs": [
                {
                    "name": "spring0",
                    "joints": [
                        // 次項を参照してください
                    ],
                    "colliderGroups": [0],
                }
            ]
        }
    }
}
```

| 名前           | 備考                                                                           |
|:---------------|:-------------------------------------------------------------------------------|
| name           | Spring名                                                                       |
| joints         | springBoneを構成する Joint のリスト                                            |
| colliderGroups | このspringに対して衝突する `VRMC_springBone.colliderGroups` の index の リスト |

#### スプリングの構成について

```
a-b-c-d
```

という joints があった場合、Runtimeでは下記のように解釈します。

```
jointNode(回転する)
| +-collision(当たり判定)
v v
a-b
b-c
c-d
```

3つの joint に展開されます。
末端の node に指定した joint のパラメータは解釈されません。
dを可動させる場合は、末端に empty node を追加してください。

```
jointNode
| +-collision
v v
a-b
b-c
c-d
d-e
```

`vrm0 の場合`

```
a-b-c-d
```

という joints があった場合、`vrm0` では下記のように解釈します。

```
jointNode(回転する)
| +-collision(当たり判定)
v v
a-b
b-c
c-d
d-(末尾に 7cm 固定の当たり判定を追加する)
```

#### joints

joints は 連続した親子関係を持つ node の集合です。
以下の制約があります。

* joints[n-1] は、 joints[n] の親または祖先であること

joints[n-1] と joints[n] が直接の親子nodeではない場合は、間の node は無視されます。
joints の最後が末端nodeではない場合は、それより子孫のnodeは無視され、個別に揺れなくなります。

> 上記の説明の通り、 joints を設定しないことによって、途中もしくは終端の node をスキップして揺れるように設定することができます。しかし、その node に他の用途がない場合は、その node は冗長となっているため、node ごと削除することをおすすめします。

### `VRMC_springBone.springs[*].joints[*]`

```json
{
    "extensions": {
        "VRMC_springBone": {
            "springs": [
                {
                    "joints": [
                        {
                            "node": 0,
                            "hitRadius": 0.1,
                            "stiffness": 0.5,
                            "gravityPower": 1.0,
                            "gravityDir": [0, -1, 0],
                            "dragForce": 0.5,
                        },
                        {
                            "node": 1,
                            // 末尾の joint は node 以外は不要です。
                        }
                    ]
                }
            ]
        }
    }
}
```

| 名前         | 値           | 備考                                     |
|:-------------|:-------------|:-----------------------------------------|
| node         | integer      | 対象の nodeの index                      |
| hitRadius    | float(meter) | springBone の当たり判定のサイズ          |
| stiffness    | [0-1]        | 剛性(初期状態に戻ろうとする力)           |
| gravityPower |              | 重力の力(SpringBoneに毎フレーム加わる力) |
| gravityDir   | [x, y, z]    | 重力方向                                 |
| dragForce    | [0-1]        | 減速(SpringBoneを減速させる力)           |

## SpringBoneのアルゴリズム

### 慣性計算

```cs
// verlet 積分で次の位置を計算
var nextTail = currentTail
    + (currentTail - prevTail) * (1.0f - dragForce) // 前フレームの移動を継続する(減衰もあるよ)
    + ParentRotation * m_localRotation * m_boneAxis * stiffnessForce // 親の回転による子ボーンの移動目標
    + external // 外力による移動量
    ;
```

### 衝突判定

TODO:

```cs
// 当たり判定と衝突を判定して衝突した場合、衝突しないところまで押しのける
```
