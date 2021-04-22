# VRMC_vrm

## 目次

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
  - [KHR_texture_transform](#khr_texture_transform)
- [Overview](#overview)
  - [JSON Schema](#json-schema)
  - [VRMCの仕様バージョン](#vrmc%E3%81%AE%E4%BB%95%E6%A7%98%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3)
  - [形式と拡張子](#%E5%BD%A2%E5%BC%8F%E3%81%A8%E6%8B%A1%E5%BC%B5%E5%AD%90)
- [glTF Schema Updates](#gltf-schema-updates)
  - [座標の単位](#%E5%BA%A7%E6%A8%99%E3%81%AE%E5%8D%98%E4%BD%8D)
  - [使わない項目](#%E4%BD%BF%E3%82%8F%E3%81%AA%E3%81%84%E9%A0%85%E7%9B%AE)
  - [保存された TANGENT を無視してもよい](#%E4%BF%9D%E5%AD%98%E3%81%95%E3%82%8C%E3%81%9F-tangent-%E3%82%92%E7%84%A1%E8%A6%96%E3%81%97%E3%81%A6%E3%82%82%E3%82%88%E3%81%84)
    - [`meshes[*].primitives[*].attributes.TANGENT`](#meshesprimitivesattributestangent)
    - [`meshes[*].primitives[*].targets.TANGENT`](#meshesprimitivestargetstangent)
  - [`meshes[*].extras.targetNames` モーフターゲットの名前(推奨)](#meshesextrastargetnames-%E3%83%A2%E3%83%BC%E3%83%95%E3%82%BF%E3%83%BC%E3%82%B2%E3%83%83%E3%83%88%E3%81%AE%E5%90%8D%E5%89%8D%E6%8E%A8%E5%A5%A8)
- [`VRMC_vrm.humanoid` ノードへのヒューマノイドボーンの割り当て(必須)](#vrmc_vrmhumanoid-%E3%83%8E%E3%83%BC%E3%83%89%E3%81%B8%E3%81%AE%E3%83%92%E3%83%A5%E3%83%BC%E3%83%9E%E3%83%8E%E3%82%A4%E3%83%89%E3%83%9C%E3%83%BC%E3%83%B3%E3%81%AE%E5%89%B2%E3%82%8A%E5%BD%93%E3%81%A6%E5%BF%85%E9%A0%88)
- [ヒューマノイドの T-Pose(必須)](#%E3%83%92%E3%83%A5%E3%83%BC%E3%83%9E%E3%83%8E%E3%82%A4%E3%83%89%E3%81%AE-t-pose%E5%BF%85%E9%A0%88)
  - [全体の制約](#%E5%85%A8%E4%BD%93%E3%81%AE%E5%88%B6%E7%B4%84)
  - [各ボーンの向き](#%E5%90%84%E3%83%9C%E3%83%BC%E3%83%B3%E3%81%AE%E5%90%91%E3%81%8D)
    - [胴](#%E8%83%B4)
    - [頭](#%E9%A0%AD)
    - [脚](#%E8%84%9A)
    - [腕](#%E8%85%95)
    - [指](#%E6%8C%87)
  - [T-Pose bake の手順](#t-pose-bake-%E3%81%AE%E6%89%8B%E9%A0%86)
- [`VRMC_vrm.meta` モデル情報(必須)](#vrmc_vrmmeta-%E3%83%A2%E3%83%87%E3%83%AB%E6%83%85%E5%A0%B1%E5%BF%85%E9%A0%88)
- [`VRMC_vrm.firstPerson` 一人称(オプション)](#vrmc_vrmfirstperson-%E4%B8%80%E4%BA%BA%E7%A7%B0%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3)
- [Expression, LookAt, SpringBone, Constraints の適用順](#expression-lookat-springbone-constraints-%E3%81%AE%E9%81%A9%E7%94%A8%E9%A0%86)
- [`VRMC_vrm.expressions` 表情(オプション)](#vrmc_vrmexpressions-%E8%A1%A8%E6%83%85%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3)
- [`VRMC_vrm.lookAt` 視線制御(オプション)](#vrmc_vrmlookat-%E8%A6%96%E7%B7%9A%E5%88%B6%E5%BE%A1%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3)
- [Known Implementations](#known-implementations)
- [Resources](#resources)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

* 進藤哲郎
* 廣瀬淳一
* 蘇柏彰
* 小渕豊

## Status

* In development

## Dependencies

Written against the glTF 2.0 spec.

* require KHR_materials_unlit extension
* require KHR_texture_transform extension
* 
* require VRMC_materials_mtoon extension
* require VRMC_springBone extension
  * VRMC_springBone require VRMC_node_collider extension
* requrie VRMC_node_constraint extension

### KHR_texture_transform

TODO: Unity の PBR シェーダーが対応するべき？

限定的に対応します。

`materials[*].pbrMetallicRoughness.baseColorTexture.extensions.KHR_texture_transform`

を使ってください。
`uv` アクセスする他のテクスチャもこの値を参照することとします。
Expression の TextureTransformBind との兼ね合いです。

## Overview

GLTFはシーンを表現するのに対して、
VRMはVRアバター向けの人間型モデル一体を表現します。

### JSON Schema

```js
{
  "extensionsUsed": {
    "VRMC_vrm"
  },
  "extensions": {
    "VRMC_vrm": {
      // VRMの拡張情報
      "specVersion": "1.0.0"
      "humanoid": {},
      "meta": {},
      "firstPerson": {},
      "expression": {},
      "lookAt": {},
    },
    "VRMC_springBone": {},
    "VRMC_node_constraint": {}
  },
  // 通常のGLTF-2.0の情報
  "materials": [
    "extensions": {
      "VMRC_materials_mtoon": {}
    }
  ],
  "nodes": [
    "extensions": {
      "VRMC_node_collider": {}
    }
  ]
}
```

* https://github.com/vrm-c/vrm-specification/tree/master/specification/VRMC_vrm-1.0_draft/schema

GLTF-2.0のJsonSchema

* https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/schema

### VRMCの仕様バージョン

```json
extensions.VRMC_vrm.specVersion  = "1.0.0"
```

### 形式と拡張子

`.glb` 形式で保存し拡張子に `.vrm` を使います。

## glTF Schema Updates

### 座標の単位

glTFの [coordinate-system-and-units](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#coordinate-system-and-units) に準拠してメートル単位です。

### 使わない項目

以下の項目を使用しません。

* animations
* cameras

### 保存された TANGENT を無視してもよい

TANGENT を正しく扱うことが技術的に困難なため、読み書きをで無視することを許容します。

#### `meshes[*].primitives[*].attributes.TANGENT`

* import: MikkTSpaceアルゴリズムで計算してください。

https://github.com/KhronosGroup/glTF/blob/master/specification/2.0/README.md#meshes

> Implementation note: When tangents are not specified, client implementations should calculate tangents using default MikkTSpace algorithms. For best results, the mesh triangles should also be processed using default MikkTSpace algorithms.

* export: MikkTSpaceアルゴリズムで計算することを期待してエクスポートしないことを推奨。

#### `meshes[*].primitives[*].targets.TANGENT`

* morphTarget で tangent がアニメーションすることは非推奨です
* export: 出力しないことを推奨
* import: 無視するを推奨

### `meshes[*].extras.targetNames` モーフターゲットの名前(推奨)

`meshes[*].primitives[*].targets.name` が無いので代替で `meshes[*].extras.targetNames` に格納します。

* https://github.com/KhronosGroup/glTF/issues/1036

## `VRMC_vrm.humanoid` ノードへのヒューマノイドボーンの割り当て(必須)

人型モデルを定義するために、人体の部位(ヒューマノイドボーン)を glTF.Node に割り当てます。

別ドキュメントにおいて仕様を示します。

[./humanoid.ja.md](./humanoid.ja.md)

## ヒューマノイドの T-Pose(必須)

VRM では、初期姿勢 T-Pose が必要です。

### 全体の制約

* Root(hipsの親)は原点
* 原点で接地している
* Z+ の方向を向いている( 0.X では Z-向き)
* 各ノードの回転・スケールがクリアされている

### 各ボーンの向き

各ボーンの曲げ軸が指定されたワールド軸になるようにしてください。

#### 胴

直立状態です。

| ボーン名前 | 曲げ軸 | 備考 |
|:-----------|:-------|------|
| hips       |        |      |
| spine      |        |      |
| chest      |        |      |
| upperChest |        |      |
| neck       |        |      |

#### 頭

正面向きです。

| ボーン名前 | 曲げ軸 | 備考                              |
|:-----------|:-------|:----------------------------------|
| head       |        |                                   |
| leftEye    |        | 視線がZ軸になるようにしてください |
| rightEye   |        | 視線がZ軸になるようにしてください |
| jaw        |        |                                   |

#### 脚

直立状態です。

| ボーン名前    | 曲げ軸 | 備考 |
|:--------------|:-------|:-----|
| leftUpperLeg  |        |      |
| leftLowerLeg  | X軸    |      |
| leftFoot      | X軸    |      |
| leftToes      | X軸    |      |
| rightUpperLeg |        |      |
| rightLowerLeg | X軸    |      |
| rightFoot     | X軸    |      |
| rightToes     | X軸    |      |

#### 腕

腕(upperArm, lowerArm, handを結ぶ線)を水平にし、掌を下に向けます。

| ボーン名前    | 曲げ軸 | 備考               |
|:--------------|:-------|:-------------------|
| leftShoulder  |        |                    |
| leftUpperArm  |        |                    |
| leftLowerArm  | Y軸    |                    |
| leftHand      |        | 掌が下になるように |
| rightShoulder |        |                    |
| rightUpperArm |        |                    |
| rightLowerArm | Y軸    |                    |
| rightHand     |        | 掌が下になるように |

#### 指

* X軸に平行にします。
* 親指を斜めにする場合、XY平面で回転してください。
  * この場合、曲げ軸は親指と垂直になります

| ボーン名前              | 曲げ軸 | 備考                     |
|:------------------------|:-------|:-------------------------|
| leftThumbProximal       | Y軸    | 人差し指に最接近した状態 |
| leftThumbIntermediate   | Y軸    | 人差し指に最接近した状態 |
| leftThumbDistal         | Y軸    | 人差し指に最接近した状態 |
| leftIndexProximal       | Z軸    |                          |
| leftIndexIntermediate   | Z軸    |                          |
| leftIndexDistal         | Z軸    |                          |
| leftMiddleProximal      | Z軸    |                          |
| leftMiddleIntermediate  | Z軸    |                          |
| leftMiddleDistal        | Z軸    |                          |
| leftRingProximal        | Z軸    |                          |
| leftRingIntermediate    | Z軸    |                          |
| leftRingDistal          | Z軸    |                          |
| leftLittleProximal      | Z軸    |                          |
| leftLittleIntermediate  | Z軸    |                          |
| leftLittleDistal        | Z軸    |                          |
| rightThumbProximal      | Y軸    | 人差し指に最接近した状態 |
| rightThumbIntermediate  | Y軸    | 人差し指に最接近した状態 |
| rightThumbDistal        | Y軸    | 人差し指に最接近した状態 |
| rightIndexProximal      | Z軸    |                          |
| rightIndexIntermediate  | Z軸    |                          |
| rightIndexDistal        | Z軸    |                          |
| rightMiddleProximal     | Z軸    |                          |
| rightMiddleIntermediate | Z軸    |                          |
| rightMiddleDistal       | Z軸    |                          |
| rightRingProximal       | Z軸    |                          |
| rightRingIntermediate   | Z軸    |                          |
| rightRingDistal         | Z軸    |                          |
| rightLittleProximal     | Z軸    |                          |
| rightLittleIntermediate | Z軸    |                          |
| rightLittleDistal       | Z軸    |                          |

### T-Pose bake の手順

T-Pose の制約

`ノードの回転・スケールをクリアする`

のためには、`Mesh`, `Skin`, `Node` の再構築が必要です。

シーンで準備

* メートルスケールにする
* T-Poseにする

処理

* mesh(skinなし)
  * 各頂点にNode.worldMatrixを乗算する
* mesh(skinあり)
  * BoneWeightの無い頂点がもしあれば、skin.root に対するBoneWeightを付与する
  * メッシュをスキニングして結果をメッシュとして再取り込みする
* Nodeの回転・スケールを除去する

により

`skins[*].inverseBindMatrices` は、

```js
[1, 0, 0, 0]
[0, 1, 0, 0]
[0, 0, 1, 0]
[-x, -y, -z, 1] // x, y, z = joint のワールド座標
```

と単純化されます。

ベースになったモデルの `inverseBindMatrices` に
回転(Z-UPの吸収)やスケール(負の値を含む)が含まれている場合があり、
これはアプリケーションで

* ボーン位置とメッシュ位置の不一致
* 面の表裏の誤判定

などの予期しない結果を誘発します。

## `VRMC_vrm.meta` モデル情報(必須)

別ドキュメントにおいて仕様を示します。

[./meta.ja.md](./meta.ja.md)

## `VRMC_vrm.firstPerson` 一人称(オプション)

VRMは、VRを想定した一人称視点の設定を定義しています。

別ドキュメントにおいて仕様を示します。

[./firstPerson.ja.md](./firstPerson.ja.md)

## Expression, LookAt, SpringBone, Constraints の適用順 

* VRMC_vrm.expression
* VRMC_vrm.lookAt
* VRMC_springBone
* VRMC_node_constraint

は Node, Mesh への変更があり実行順の影響があります。
推奨される更新の適用順は下記のとおりです。

1. ヒューマノイドボーンを解決
2. 頭の位置が決まるのでLookAtを解決
  * Bone型 => leftEye, rightEye ボーンを回転
  * Expression型 => 次項
3. ExpressionUpdate
  * 喜怒哀楽 コントローラーなど外部入力 => Expression ウェイトを設定
  * LipSync => Expression ウェイトを設定
  * AutoBlink => Expression ウェイトを設定
  * Expression型のLookAt => Expression のウェイトを設定
4. Expression を Apply する
5. コンストレイントを解決
6. SpringBoneを解決

## `VRMC_vrm.expressions` 表情(オプション)

VRMは、ヒューマノイド向けに Expression を定義しています。

> VRM-0 仕様で使っていた BlendShape という言葉は MorphTarget と同じものを指しており意味が異なるため、 BlendShape から Expression に改名しました。

別ドキュメントにおいて仕様を示します。

[./expressions.ja.md](./expressions.ja.md)

## `VRMC_vrm.lookAt` 視線制御(オプション)

VRMは、ヒューマノイド向けに視線制御を定義しています。

別ドキュメントにおいて仕様を示します。

[./lookAt.ja.md](./lookAt.ja.md)

## Known Implementations

* https://vrm.dev/vrm_applications/

## Resources

* https://vrm.dev/
