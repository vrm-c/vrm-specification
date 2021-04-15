# VRMC_springBone

## Contributors

* 進藤哲郎
* 蘇柏彰
* 小渕豊

## Status

Draft

## Dependencies

Written against the glTF 2.0 spec.

* Require VRMC_node_collider extension

## Overview

簡易な物理風のアニメーションシステム。
髪や衣装が揺れるような見た目に対する用途を想定している。

```json
{
  "extensionsUsed": [
    "VRMC_springBone"
  ],
  "extensions": {
    "VRMC_springBone": {
      // springBone の配列
      "springs": [
        {
            // springBone
        }
      ]
    }
  },
  // 通常のGLTF-2.0の情報
  "materials": [
    {
      "extensions": {
        "VMRC_materials_mtoon": {}
      }
    }
  ],
  "nodes": [
    {
      "extensions": {
        "VRMC_node_collider": {}
      }
    }
  ]
}
```

### `VRMC_SpringBone.springs[*]` SpringBone 一本の情報

| 名前      | 備考                                        |
|:----------|:--------------------------------------------|
| name      | Spring名                                    |
| joints    | springBoneを構成する Joint のリスト。       |
| colliders | このspringに対して衝突する node の リスト。 |

#### joints

joints は 連続した親子関係を持つ node の集合です。
以下の制約があります。

* joints[n-1] は、 joints[n] の親または祖先であること

joints[n-1] と joints[n] が直接の親子nodeではない場合は、間の node は無視されます。
joints の最後が末端nodeではない場合は、それより子孫のnodeは無視され、個別に揺れなくなります。

> 上記の説明の通り、 joints を設定しないことによって、途中もしくは終端の node をスキップして揺れるように設定することができます。しかし、その node に他の用途がない場合は、その node は冗長となっているため、node ごと削除することをおすすめします。

#### colliders

`VRMC_SpringBone.springs[*].colliders[*].node` に対象の node の index を指定します。
対象の node には、 `nodes[*].extensions.VRMC_node_collider` を設定してください。

### `VRMC_SpringBone.springs[*].joints[*]` SpringBone を構成する Joint の情報

| 名前         | 値           | 備考                                     |
|:-------------|:-------------|:-----------------------------------------|
| node         | integer      | 対象の nodeの index                      |
| hitRadius    | float(meter) | springBone の当たり判定のサイズ          |
| stiffness    | [0-1]        | 剛性                                     |
| gravityPower |              | 重力の力(SpringBoneに毎フレーム加わる力) |
| gravityDir   | [x, y, z]    | 重力方向                                 |
| dragForce    | [0-1]        | 減速(SpringBoneを減速させる力)           |

## SpringBoneのアルゴリズム

TODO:

```cs
// verlet 積分で次の位置を計算
var nextTail = currentTail
    + (currentTail - prevTail) * (1.0f - dragForce) // 前フレームの移動を継続する(減衰もあるよ)
    + ParentRotation * m_localRotation * m_boneAxis * stiffnessForce // 親の回転による子ボーンの移動目標
    + external // 外力による移動量
    ;
```

TODO:

```cs
// 当たり判定と衝突を判定して衝突した場合、衝突しないところまで押しのける
```
