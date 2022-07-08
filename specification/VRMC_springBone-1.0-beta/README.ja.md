# VRMC_springBone

*Version 1.0-beta*

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [Overview](#overview)
- [構成](#%E6%A7%8B%E6%88%90)
  - [用語](#%E7%94%A8%E8%AA%9E)
    - [SpringJoint](#springjoint)
    - [HeadSpringJoint と TailSpringJoint のペア](#headspringjoint-%E3%81%A8-tailspringjoint-%E3%81%AE%E3%83%9A%E3%82%A2)
    - [SpringChain](#springchain)
  - [例](#%E4%BE%8B)
  - [vrm0 の動作](#vrm0-%E3%81%AE%E5%8B%95%E4%BD%9C)
  - [例外](#%E4%BE%8B%E5%A4%96)
    - [ある SpringJoint が、重複して複数の SpringChain に所属すること(禁止)](#%E3%81%82%E3%82%8B-springjoint-%E3%81%8C%E9%87%8D%E8%A4%87%E3%81%97%E3%81%A6%E8%A4%87%E6%95%B0%E3%81%AE-springchain-%E3%81%AB%E6%89%80%E5%B1%9E%E3%81%99%E3%82%8B%E3%81%93%E3%81%A8%E7%A6%81%E6%AD%A2)
    - [分岐する SpringChain (未定義)](#%E5%88%86%E5%B2%90%E3%81%99%E3%82%8B-springchain-%E6%9C%AA%E5%AE%9A%E7%BE%A9)
- [評価する座標系](#%E8%A9%95%E4%BE%A1%E3%81%99%E3%82%8B%E5%BA%A7%E6%A8%99%E7%B3%BB)
  - [Center Space](#center-space)
- [JSON](#json)
  - [`VRMC_springBone.specVersion`](#vrmc_springbonespecversion)
  - [`VRMC_springBone.colliders`](#vrmc_springbonecolliders)
  - [`VRMC_springBone.colliderGroups`](#vrmc_springbonecollidergroups)
  - [`VRMC_springBone.springs`](#vrmc_springbonesprings)
    - [joints](#joints)
  - [`VRMC_springBone.springs[*].joints[*]`](#vrmc_springbonespringsjoints)
- [SpringBoneのアルゴリズム](#springbone%E3%81%AE%E3%82%A2%E3%83%AB%E3%82%B4%E3%83%AA%E3%82%BA%E3%83%A0)
  - [更新順序](#%E6%9B%B4%E6%96%B0%E9%A0%86%E5%BA%8F)
  - [初期化](#%E5%88%9D%E6%9C%9F%E5%8C%96)
  - [更新処理](#%E6%9B%B4%E6%96%B0%E5%87%A6%E7%90%86)
    - [慣性計算](#%E6%85%A3%E6%80%A7%E8%A8%88%E7%AE%97)
    - [コライダーとの衝突](#%E3%82%B3%E3%83%A9%E3%82%A4%E3%83%80%E3%83%BC%E3%81%A8%E3%81%AE%E8%A1%9D%E7%AA%81)
    - [回転への反映](#%E5%9B%9E%E8%BB%A2%E3%81%B8%E3%81%AE%E5%8F%8D%E6%98%A0)
    - [Center spaceの考慮](#center-space%E3%81%AE%E8%80%83%E6%85%AE)

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

ノードに慣性による速度維持と元の姿勢に戻ろうとするバネのプロシージャルアニメーションを実装します。
髪や衣装などが揺れるような、見た目に対する用途を想定しています。
剛性、減速、重力等のパラメーターで挙動を調整できます。
また、SpringBone の各節の末端(球) と、 当たり判定ノード(collider: 球・カプセル)の衝突を設定できます。

## 構成

### 用語
説明のため以下の用語を導入します。

| 用語                                      | 意味                 | json                                             |
|-------------------------------------------|----------------------|--------------------------------------------------|
| SpringJoint                               | 設定をしたnode       | `springs[i].joints[j]`                           |
| HeadSpringJoint と TailSpringJoint のペア | 連続するjoint2つの組 | `springs[i].joints[j]`, `springs[i].joints[j+1]` |
| SpringChain                               | 連続するjoint全体    | `springs[i]`                                     |

#### SpringJoint

SpringBoneの設定を付与する、単一のglTFのノードです。

#### HeadSpringJoint と TailSpringJoint のペア

Head(`SpringJoint`) と Tail(`SpringJoint`) のペアで表される区間。
Tail で前フレームとの位置差分の計算します。
設定がある場合はコリジョンとの衝突も計算します。
必ず Tail の祖先に Head が存在する必要がありますが、
Head の直接の親でなくてもかまいません。

```
  揺れる
<- Tail ->
    |
    |
    o
   Head
```

Tail の移動から Head の回転を算出します。

#### SpringChain

一連の連続した `SpringJoint` により構成される鎖状の集合です。

```
a-b-c-d
```

### 例

```
a-b-c-d
```

という joints があった場合、下記のように解釈します。

```
Head(回転する)
| +-Tail(移動差分・当たり判定)
v v
a-b
b-c
c-d
```

3つの `HeadSpringJoint と TailSpringJoint のペア` に展開されます。
末端の SpringJoint は、Tail としてのみ使われるので SpringJoint のパラメータは使用されません。
dを回転させたい場合は、以下のように末端に Node を追加して SpringJoint 設定をしてください。
追加する Node は empty でかまいません。

```
Head(回転する)
| +-Tail(移動差分・当たり判定)
v v
a-b
b-c
c-d
d-e
```

### vrm0 の動作

```
a-b-c-d
```

という joints があった場合、`vrm0` では下記のように解釈します。

```
Head(回転する)
| +-Tail(移動差分・当たり判定)
v v
a-b
b-c
c-d
d-(末尾 7cm の遠さに SpringJoint を追加する)
```

### 例外

#### ある SpringJoint が、重複して複数の SpringChain に所属すること(禁止)

個々の `SpringChain` は同じ joint を含んではいけない。
以下の場合、b, c, d が同時に２つの SpringChain に所属しています。

```
b-c-d

a-b-c-d
```

以下のような構成は、暗黙裡に重複すると見なします。
b, c が同時に２つの SpringChain に所属しています。
スキップされていても重複判定の対象になります。

```
() はスキップされる node
a-(b)-c

b-(c)-e
```

#### 分岐する SpringChain (未定義)

- 別々の `SpringBoneChain` として扱ってください

```
  x-y-z
  |
a-b-c-d
```

* `a-b-c-d` と `x-y-z` の２本の SpringChain として処理する。
* `a-b-c-d` と `x-y-z` のどちらを先に処理するか、移動差分をどのタイミングで得るかなどは特に指定せずに未定義とします。実装によって挙動が異なる可能性があります。並列実行など実装の都合を優先してよい。

## 評価する座標系

SpringBoneにおいて、Jointの位置の評価には、原則としてWorld Spaceを用います。

### Center Space

`center` プロパティを利用することによって、Jointの位置の評価にWorld Space以外を用いることが可能です。
`center` はモデル内の1ノードを指定することができ、指定したノードから相対となるSpaceでJointの位置が評価されます。
`center` は、SpringChain単位で指定します。

Centerノードは、そのSpringChainの0番目のJointもしくは、その祖先nodeである必要があります。
また、Centerノードには、他のSpringChainのJointノードおよびその子孫を指定することはできません。

以下のような、主にSpringBoneが揺れすぎてしまう場合に有効です。

- モデルが歩行などによって平行移動した場合に、SpringBoneが揺れすぎてしまう
- 頭についているSpringBone（例えば、髪の毛や髪飾り）について、頭を動かした場合のみレスポンシブに動いてほしい

## JSON

```json
{
    "extensionsUsed": [
        "VRMC_springBone"
    ],
    "extensions": {
        "VRMC_springBone": {
            // collider の配列
            "specVersion": "1.0-beta",
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
extensions.VRMC_springBone.specVersion = "1.0-beta"
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
                    "center": 0
                }
            ]
        }
    }
}
```

| 名前           | 備考                                                               |
|:---------------|:------------------------------------------------------------------|
| name           | Spring名                                                           |
| joints         | springBoneを構成する Joint のリスト                                       |
| colliderGroups | このspringに対して衝突する `VRMC_springBone.colliderGroups` の index の リスト |
| center         | [Center Space](#center-space) のルートとして用いるノードのインデックス                |

#### joints

`SpringBoneChain` を表します。
以下の制約があります。

* joints[n] は joints[n+1] の親または祖先であること

joints[n] と joints[n+1] が直接の親子nodeではない場合は、間の node は無視されます。
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

> *このセクションはnon-normativeです。*

このセクションでは、SpringBoneのリファレンス実装を示します。

実際の実装は、UniVRMやthree-vrmなど、VRMの各実装のソースコードに見ることができます。

SpringBoneのリファレンス実装は、Verlet積分を用いて簡易的な物理シミュレーションを行うものです。

### 更新順序

SpringBone系全体の更新は、SpringBoneJoint同士の依存を解決しながら行われます。
具体的には、Jointの親にJointが存在する場合、その親が優先して処理されます。
すなわち、根本から先端にかけて順番に更新処理が行われます。

### 初期化

本実装で扱うひとつのSpringBoneJointは、以下のようなステートを持ちます:

```ts
interface SpringBoneJointState {
    prevTail: Vector3;
    currentTail: Vector3;
    boneAxis: Vector3;
    boneLength: number;
    initialLocalMatrix: Matrix4;
    initialLocalRotation: Quaternion;
}
```

`prevTail` ・ `currentTail` は、そのJointが対象とする子Nodeの、ワールド空間における位置を表します。
`currentTail` が現在のフレームの位置、 `prevTail` が1フレーム前の位置を表します。

`boneAxis` は、そのJointが対象とする子Nodeの、ローカル空間におけるレスト状態の伸びる方向を表します。
`boneLength` は、そのJointが対象とする子Nodeの、ワールド空間における長さを表します。

`initialLocalMatrix` は、そのJointが対象とするNodeのレスト状態のトランスフォームを表します。
`initialLocalRotation` は、そのJointが対象とするNodeのレスト状態の回転を表します。

### 更新処理

大雑把に、以下の3ステップで更新がされます:

- 慣性計算
- コライダーとの衝突
- 回転への反映

#### 慣性計算

以下の3つの力を計算し、 `currentTail` の位置を更新します。

- 慣性: tailが慣性で動こうとする力。 `1.0 - dragForce` が係数となります。
- 剛性: tailが元の向きに戻ろうとする力。 `stiffnessForce` が係数となります。
- 重力: tailが重力に引っ張られる力。 `gravityDir * gravityPower` が係数となります。

以下の擬似コードに処理を表します:

```ts
var {currentTail, prevTail, boneAxis, boneLength} = state;
var {dragForce, stiffnessForce, gravityDir, gravityPower} = props;

var worldPosition = node.worldPosition;
var localRotation = node.rotation;
var parentWorldRotation = node.parent ? node.parent.worldRotation : Quaternion.identity;

// verlet 積分で次の位置を計算
var inertia = (currentTail - prevTail) * (1.0f - dragForce);
var stiffness = deltaTime * parentWorldRotation * localRotation * boneAxis * stiffnessForce;
var external = deltaTime * gravityDir * gravityPower;

var nextTail = currentTail + inertia + stiffness + external;

// 長さの制約
nextTail = worldPosition + (nextTail - worldPosition).normalized * boneLength;
```

#### コライダーとの衝突

Jointが対象とするColliderとの衝突判定を行います。
Colliderそれぞれに対して距離の判定を行い、距離がColliderとJointの半径の和より小さかった場合は、それらが接する位置までジョイントを押しのける処理を行います。

以下の擬似コードに処理を表します:

```ts
for (var collider of colliders) {
    var {dir, dist} = collider.calculateCollision(nextTail);

    if (dist < 0.0) {
        // 押しのける
        nextTail = nextTail - dir * dist;

        // 長さの制約
        nextTail = worldPosition + (nextTail - worldPosition).normalized * boneLength;
    }
}
```

#### 回転への反映

上記の計算で得られた新しい `currentTail` をもとに、Jointが対象とするNodeの回転を更新します。

以下の擬似コードに処理を表します:

```ts
// prevTail・currentTailの更新
prevTail = currentTail;
currentTail = nextTail;

// 回転の更新
var to = (nextTail * (node.parent.worldMatrix * initialLocalMatrix).inverse).normalized;
node.rotation = initialLocalRotation * Quaternion.fromToQuaternion(boneAxis, to);
```

#### Center spaceの考慮

SpringBoneに `center` が設定されている場合、SpringBoneの慣性は[Center Space](#center-space)で評価されます。
これは、上記の慣性計算において、World Spaceで評価していたトランスフォームを代わりにCenter Spaceで評価することで実現できます。
外力（重力）については、 `center` の設定に依らずWorld Spaceで計算されます。
