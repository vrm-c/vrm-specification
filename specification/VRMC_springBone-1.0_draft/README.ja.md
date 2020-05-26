# VRMC_springBone

## Contributors

* TODO: Name, affiliation, and contact info for each contributor

## Status

Draft

## Dependencies

Written against the glTF 2.0 spec.

## Overview

髪や衣装が揺れるような見た目専用を想定した、
Physicsエンジンによらない揺れもの。

| 名前           | 備考                  |
|:---------------|:----------------------|
| boneGroups     | SpringBoneのリスト    |
| colliderGroups | ColliderGroupのリスト |

### SpringBone

| 名前           | 備考                                           |
|:---------------|:-----------------------------------------------|
| comment        | 自由文字列                                     |
| stiffness      | 剛性。硬さ。初期姿勢に戻ろうとする力           |
| gravityPower   | 重力加速度                                     |
| gravityDir     | 重力方向。下以外に重力を働かせることができます |
| dragForce      | 空気抵抗。減速する力                           |
| center         | Local座標となるNodeのindex                     |
| hitRadius      | SpringBoneの当たり判定の半径                   |
| bones          | SpringBoneを設定するNodeのindexの配列          |
| colliderGroups | 当たり判定を処理する colliderGroup のindex     |

### ColliderGroup

| 名前      | 備考                      |
|:----------|:--------------------------|
| node      | Colliderの設置Nodeのindex |
| colliders | Colliderのリスト          |

### Collider

* SpringBone専用で、Physicsシステムとは独立していることを想定しています。

| 名前      | 備考                                                                        |
|:----------|:----------------------------------------------------------------------------|
| shapeType | Colliderの形状。sphere, capsule                                             |
| offset    | ColliderのNodeからのローカル位置                                            |
| rotation  | ColliderのNodeからのローカル回転。Euler角Radians                            |
| size      | Colliderの半径。sphereのときは {radians}, capsuleのときは {radians, length} |

### SpringBoneのアルゴリズム

TODO:

```cs
// verlet 積分で次の位置を計算
var nextTail = currentTail
    + (currentTail - prevTail) * (1.0f - dragForce) // 前フレームの移動を継続する(減衰もあるよ)
    + ParentRotation * m_localRotation * m_boneAxis * stiffnessForce // 親の回転による子ボーンの移動目標
    + external // 外力による移動量
    ;
```
