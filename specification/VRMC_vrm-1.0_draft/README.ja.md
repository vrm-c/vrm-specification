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
  - [MeshにTANGENTを保存しない](#mesh%E3%81%ABtangent%E3%82%92%E4%BF%9D%E5%AD%98%E3%81%97%E3%81%AA%E3%81%84)
  - [`meshes[*].extras.targetNames` モーフターゲットの名前(推奨)](#meshesextrastargetnames-%E3%83%A2%E3%83%BC%E3%83%95%E3%82%BF%E3%83%BC%E3%82%B2%E3%83%83%E3%83%88%E3%81%AE%E5%90%8D%E5%89%8D%E6%8E%A8%E5%A5%A8)
- [`VRMC_vrm.humanoid` ノードへのヒューマノイドボーンの割り当て(必須)](#vrmc_vrmhumanoid-%E3%83%8E%E3%83%BC%E3%83%89%E3%81%B8%E3%81%AE%E3%83%92%E3%83%A5%E3%83%BC%E3%83%9E%E3%83%8E%E3%82%A4%E3%83%89%E3%83%9C%E3%83%BC%E3%83%B3%E3%81%AE%E5%89%B2%E3%82%8A%E5%BD%93%E3%81%A6%E5%BF%85%E9%A0%88)
  - [ヒューマノイドボーンの一覧](#%E3%83%92%E3%83%A5%E3%83%BC%E3%83%9E%E3%83%8E%E3%82%A4%E3%83%89%E3%83%9C%E3%83%BC%E3%83%B3%E3%81%AE%E4%B8%80%E8%A6%A7)
  - [ボーンの親子関係](#%E3%83%9C%E3%83%BC%E3%83%B3%E3%81%AE%E8%A6%AA%E5%AD%90%E9%96%A2%E4%BF%82)
- [ヒューマノイドの T-Pose(必須)](#%E3%83%92%E3%83%A5%E3%83%BC%E3%83%9E%E3%83%8E%E3%82%A4%E3%83%89%E3%81%AE-t-pose%E5%BF%85%E9%A0%88)
  - [全体の制約](#%E5%85%A8%E4%BD%93%E3%81%AE%E5%88%B6%E7%B4%84)
  - [各ボーンの向き](#%E5%90%84%E3%83%9C%E3%83%BC%E3%83%B3%E3%81%AE%E5%90%91%E3%81%8D)
  - [T-Pose bake の手順](#t-pose-bake-%E3%81%AE%E6%89%8B%E9%A0%86)
- [`VRMC_vrm.meta` モデル情報(必須)](#vrmc_vrmmeta-%E3%83%A2%E3%83%87%E3%83%AB%E6%83%85%E5%A0%B1%E5%BF%85%E9%A0%88)
  - [モデル名・作者など](#%E3%83%A2%E3%83%87%E3%83%AB%E5%90%8D%E3%83%BB%E4%BD%9C%E8%80%85%E3%81%AA%E3%81%A9)
  - [アバターの人格に関する許容範囲](#%E3%82%A2%E3%83%90%E3%82%BF%E3%83%BC%E3%81%AE%E4%BA%BA%E6%A0%BC%E3%81%AB%E9%96%A2%E3%81%99%E3%82%8B%E8%A8%B1%E5%AE%B9%E7%AF%84%E5%9B%B2)
  - [再配布・改変に関する許容範囲](#%E5%86%8D%E9%85%8D%E5%B8%83%E3%83%BB%E6%94%B9%E5%A4%89%E3%81%AB%E9%96%A2%E3%81%99%E3%82%8B%E8%A8%B1%E5%AE%B9%E7%AF%84%E5%9B%B2)
- [`VRMC_vrm.firstPerson` 一人称(オプション)](#vrmc_vrmfirstperson-%E4%B8%80%E4%BA%BA%E7%A7%B0%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3)
  - [MeshAnnotation](#meshannotation)
  - [MeshAnnotation(enum)](#meshannotationenum)
  - [MeshAnnotation.Autoのアルゴリズム](#meshannotationauto%E3%81%AE%E3%82%A2%E3%83%AB%E3%82%B4%E3%83%AA%E3%82%BA%E3%83%A0)
- [Expression, LookAt, SpringBone, Constraints の適用順](#expression-lookat-springbone-constraints-%E3%81%AE%E9%81%A9%E7%94%A8%E9%A0%86)
- [`VRMC_vrm.expressions` Expression(オプション)](#vrmc_vrmexpressions-expression%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3)
  - [Expressionの仕様](#expression%E3%81%AE%E4%BB%95%E6%A7%98)
  - [ExpressionPreset(enum)](#expressionpresetenum)
  - [Expression 更新のアルゴリズム](#expression-%E6%9B%B4%E6%96%B0%E3%81%AE%E3%82%A2%E3%83%AB%E3%82%B4%E3%83%AA%E3%82%BA%E3%83%A0)
- [`VRMC_vrm.lookAt` 視線制御(オプション)](#vrmc_vrmlookat-%E8%A6%96%E7%B7%9A%E5%88%B6%E5%BE%A1%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3)
  - [LookAtType](#lookattype)
  - [水平内外、垂直上下](#%E6%B0%B4%E5%B9%B3%E5%86%85%E5%A4%96%E5%9E%82%E7%9B%B4%E4%B8%8A%E4%B8%8B)
  - [LookAtのアルゴリズム](#lookat%E3%81%AE%E3%82%A2%E3%83%AB%E3%82%B4%E3%83%AA%E3%82%BA%E3%83%A0)
- [Known Implementations](#known-implementations)
- [Resources](#resources)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

* 進藤哲郎
* 廣瀬淳一
* 蘇柏彰
* FMS-Cat

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
* requrie VRMC_constraints extension

### KHR_texture_transform

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
    "VRMC_constraints": {}
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

### MeshにTANGENTを保存しない

NormalMapが存在する場合は、MikkTSpaceアルゴリズムで計算すること

* meshes[*].primitives[*].attributes.TANGENT
* meshes[*].primitives[*].targets.TANGENT

### `meshes[*].extras.targetNames` モーフターゲットの名前(推奨)

`meshes[*].primitives[*].targets.name` が無いので代替で `meshes[*].extras.targetNames` に格納します。

* https://github.com/KhronosGroup/glTF/issues/1036

## `VRMC_vrm.humanoid` ノードへのヒューマノイドボーンの割り当て(必須)

人型モデルを定義するために、人体の部位(ヒューマノイドボーン)を glTF.Node に割り当てます。

```json
extensions.VRMC_vrm.humanoid.humanBones = {
  "hips": { 
    "node": 1 // index of glTF.Node
  },
  // 省略
}
```

### ヒューマノイドボーンの一覧

#### 胴

| ボーン名前 | 必須 | 親ボーン   | 位置の目安 | 備考                                                     |
|:-----------|:-----|:-----------|------------|----------------------------------------------------------|
| hips       | 必須 | (root)     | 股間       | 通常、このボーンだけが移動し、他のボーンは回転だけします |
| spine      | 必須 | hips       | 骨盤の上端 |                                                          |
| chest      |      | spine      | 胸郭の下端 | 0.X では必須だった                                       |
| upperChest |      | chest      |            |                                                          |
| neck       |      | upperChest | 首の付け根 | 0.X では必須だった                                       |

#### 頭

| ボーン名前 | 必須 | 親ボーン | 位置の目安 | 備考                       |
|:-----------|:-----|:---------|------------|----------------------------|
| head       | 必須 | neck     | 首の上端   |                            |
| leftEye    |      | head     |            | 視線をボーンで動かすモデル |
| rightEye   |      | head     |            | 視線をボーンで動かすモデル |
| jaw        |      | head     |            |                            |

#### 脚

| ボーン名前    | 必須 | 親ボーン      | 位置の目安     | 備考 |
|:--------------|:-----|:--------------|----------------|------|
| leftUpperLeg  | 必須 | hips          | 脚の付け根     |      |
| leftLowerLeg  | 必須 | leftUpperLeg  | 膝             |      |
| leftFoot      | 必須 | leftLowerLeg  | 足首           |      |
| leftToes      |      | leftFoot      | 足の指の付け根 |      |
| rightUpperLeg | 必須 | hips          | 脚の付け根     |      |
| rightLowerLeg | 必須 | rightUpperLeg | 膝             |      |
| rightFoot     | 必須 | rightLowerLeg | 足首           |      |
| rightToes     |      | rightFoot     | 足の指の付け根 |      |

#### 腕

| ボーン名前    | 必須 | 親ボーン      | 位置の目安   | 備考 |
|:--------------|:-----|:--------------|--------------|------|
| leftShoulder  |      | chest         |              |      |
| leftUpperArm  | 必須 | leftShoulder  | 上腕の付け根 |      |
| leftLowerArm  | 必須 | leftUpperArm  | 肘           |      |
| leftHand      | 必須 | leftLowerArm  | 手首         |      |
| rightShoulder |      | chest         |              |      |
| rightUpperArm | 必須 | rightShoulder | 上腕の付け根 |      |
| rightLowerArm | 必須 | rightUpperArm | 肘           |      |
| rightHand     | 必須 | rightLowerArm | 手首         |      |

#### 指

| ボーン名前              | 必須 | 親ボーン                | 位置の目安 | 備考 |
|:------------------------|:-----|:------------------------|------------|------|
| leftThumbProximal       |      | leftHand                |            |      |
| leftThumbIntermediate   |      | leftThumbProximal       |            |      |
| leftThumbDistal         |      | leftThumbIntermediate   |            |      |
| leftIndexProximal       |      | leftHand                |            |      |
| leftIndexIntermediate   |      | leftIndexProximal       |            |      |
| leftIndexDistal         |      | leftIndexIntermediate   |            |      |
| leftMiddleProximal      |      | leftHand                |            |      |
| leftMiddleIntermediate  |      | leftMiddleProximal      |            |      |
| leftMiddleDistal        |      | leftMiddleIntermediate  |            |      |
| leftRingProximal        |      | leftHand                |            |      |
| leftRingIntermediate    |      | leftRingProximal        |            |      |
| leftRingDistal          |      | leftRingIntermediate    |            |      |
| leftLittleProximal      |      | leftHand                |            |      |
| leftLittleIntermediate  |      | leftLittleProximal      |            |      |
| leftLittleDistal        |      | leftLittleIntermediate  |            |      |
| rightThumbProximal      |      | rightHand               |            |      |
| rightThumbIntermediate  |      | rightThumbProximal      |            |      |
| rightThumbDistal        |      | rightThumbIntermediate  |            |      |
| rightIndexProximal      |      | rightHand               |            |      |
| rightIndexIntermediate  |      | rightIndexProximal      |            |      |
| rightIndexDistal        |      | rightIndexIntermediate  |            |      |
| rightMiddleProximal     |      | rightHand               |            |      |
| rightMiddleIntermediate |      | rightMiddleProximal     |            |      |
| rightMiddleDistal       |      | rightMiddleIntermediate |            |      |
| rightRingProximal       |      | rightHand               |            |      |
| rightRingIntermediate   |      | rightRingProximal       |            |      |
| rightRingDistal         |      | rightRingIntermediate   |            |      |
| rightLittleProximal     |      | rightHand               |            |      |
| rightLittleIntermediate |      | rightLittleProximal     |            |      |
| rightLittleDistal       |      | rightLittleIntermediate |            |      |

### ボーンの親子関係

* ボーンの間にボーンではないノードが入ることは許容します(UpperLegとLowerLegの間にボーンでないノードがあるなど)

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

### モデル名・作者など

| 名前               | 値                     | 備考                                                                                                                                                                      |
|:-------------------|:-----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name               | string 必須            | アバターモデル名称                                                                                                                                                        |
| version            | string 必須            | アバターモデルのバージョン                                                                                                                                                |
| authors            | string[] 必須          | 作者名（複数名を併記可）。アバターの造形者 ・代表者名を最初に書くことを推奨、複数系（s）で名称を表記                                                                      |
| copyrights         | string                 | 著作権者。Authorsと明確に分ける                                                                                                                                           |
| contactInformation | string                 | 作者（代表者）への連絡先                                                                                                                                                  |
| reference          | string                 | アバターの「親作品」となるようなものがあれば、その情報                                                                                                                    |
| thumbnailImage     | gltf.images への index | アバターモデルのサムネイルにアクセスするためのgltf.imagesへのindex。1024x1024程度の解像度テクスチャを推奨します。正方形の画像は必須です。アプリのアイコンとして使かいます |

### アバターの人格に関する許容範囲

| 名前                      | 値                                                                                           | 備考                                           |
|---------------------------|----------------------------------------------------------------------------------------------|------------------------------------------------|
| avatarPermission          | OnlyAuthor, ExplicitlyLicensedPerson, Everyone                                               | アバターに人格を与えることの許諾範囲           |
| violentUsage              | bool                                                                                         | このアバターを用いて暴力表現を演じることの許可 |
| sexualUsage               | bool                                                                                         | このアバターを用いて性的表現を演じることの許可 |
| gameUsage                 | bool                                                                                         | アバターを操作することの許諾範囲               |
| commercialUsage           | PersonalNonCommercialNonProfit, PersonalNonCommercialProfit, PersonalCommercial, Corporation | 商用利用の許可                                 |
| politicalOrReligiousUsage | bool                                                                                         | 政治的・宗教的目的での利用                     |

#### otherPermissionUrl
上記許諾条件以外のライセンス条件がある場合はそのライセンス文書へのURLを記述

### 再配布・改変に関する許容範囲

| 名前                | 値                                | 備考           |
|:--------------------|:----------------------------------|----------------|
| creditNotation      | Required,Unnecessary,Abandoned    | クレジット表記 |
| allowRedistribution | bool                              | 再配布         |
| modify              | Prohibited,Inherited,NotInherited | 改変           |

## `VRMC_vrm.firstPerson` 一人称(オプション)

VRMは、VRを想定した一人称視点の設定を定義しています。
lookAt.offsetFromHeadBone を HMDの参考位置に利用できます。

### MeshAnnotation

Mesh単位での描画を制御します。

`extensions.VRMC_vrm.firstPerson.meshAnnotations[*]`

| 名前            | 備考                                |
|:----------------|:------------------------------------|
| node            | 対象node(meshを持っている)へのindex |
| firstPersonFlag | 後述                                |

firstPersonFlag。VRアプリでモデルを使用した場合に、自モデルの描画をHMDとそれ以外で切り分けます。

### MeshAnnotation(enum)

`thirdPersonOnly` は、VR描画時に自分の頭部を非表示にすることを意図しています。VR視点カメラは、頭部の中に位置することが想定されます。そのため、顔・頭・髪のポリゴンが視界に見えてしまうのを防ぎます。

特に描き分ける必要ないオブジェクトは、 `both` にして、すべてのカメラから描画されるようにします。

`firstPersonOnly` は、自分だけに見えて他人視点からは見えない設定です。アプリ側で特別な用途が無ければ使いません。

`auto` はMeshを頭部(`thirdPersonOnly`)とその他(`both`)に分割してください。次の節で詳述します。

| 名前            | 1人称カメラ(VR視点) | その他のカメラ | 例                                                                 |
|:----------------|:--------------------|:---------------|--------------------------------------------------------------------|
| thirdPersonOnly | 描画しない          | 描画する       | 顔・目・頭・髪、帽子・ヘルメットなどVR視界を遮るもの               |
| firstPersonOnly | 描画する            | 描画しない     | プレイヤーだけに見えてたの視点からは見えないユーザーインタフェース |
| both            | 描画する            | 描画する       |                                                                    |

### MeshAnnotation.Autoのアルゴリズム

* Meshの頂点をすべて調べて、Headボーンとその子孫のボーンに対するWeightを持つ頂点を集める
* 上記の頂点を含む三角形と含まない三角形に２分したMeshを作成する
* 上記の頂点を含むMeshをThirdPersonOnly、含まないMeshをBothとしたに設定する


## Expression, LookAt, SpringBone, Constraints の適用順 

* VRMC_vrm.expression
* VRMC_vrm.lookAt
* VRMC_springBone
* VRMC_constraints

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

## `VRMC_vrm.expressions` Expression(オプション)

VRMは、ヒューマノイド向けに Expression を定義しています。

> VRM-0 仕様で使っていた BlendShape という言葉は MorphTarget と同じものを指しており意味が異なるため、 BlendShape から Expression に改名しました。

Expression は、

* MorphTarget
* MaterialColor
* TextureTransform

のグループに対して意味を指定する機能です。

> 例えば、 `口をへの字にする MorphTarget` と `目を閉じる MorphTarget` の組み合わせを `sad` にするなど

### Expressionの仕様

| 名前                                 | 備考                                                                               |
|:-------------------------------------|:-----------------------------------------------------------------------------------|
| expressions[*].preset                | 上記のExpressionを一意に識別するために以下の制約に従ってください                   |
| expressions[*].name                  | 上記のExpressionを一意に識別するために以下の制約に従ってください                   |
| expressions[*].isBinary              | trueの場合 value!=0 を 1 とみなします                                              |
| expressions[*].morphTargetBinds      | MorphTargetBind(後述) のリスト                                                     |
| expressions[*].materialColorBinds    | MaterialValueBind(後述) のリスト                                                   |
| expressions[*].textureTransformBinds | TextureTransformBind(後述) のリスト                                                |
| expressions[*].overrideMouth         | このExpressionのWeightが0でないときに、リップシンク(後述) のウェイトを操作します。 |
| expressions[*].overrideBlink         | このExpressionのWeightが0でないときに、瞬き(後述) のウェイトを操作します。         |
| expressions[*].overrideLookAt        | このExpressionのWeightが0でないときに、視線(後述) のウェイトを操作します。         |

#### Expression の識別

各Expressionを一意に識別するために以下の制約に従ってください

preset が custom 以外の時

* preset を重複させない
* name を 空文字列にする

preset が custom の時

* preset custom は重複してよい
* name を重複させない

### ExpressionPreset(enum)

#### ユーザー定義(enum)

| 名前   | 備考                                                                                    |
|:-------|:----------------------------------------------------------------------------------------|
| custom | Applicationが独自に決めたExpressionを使う場合などに使用します。nameで識別してください。 |

#### 感情(enum)

| 名前      | 備考                   |
|:----------|:-----------------------|
| happy     | 喜。 `joy` から変更    |
| angry     | 怒                     |
| sad       | 哀。 `sorrow` から変更 |
| relaxed   | 楽。 `fun` から変更    |
| surprised | 驚。 `1.0で新規追加`   |

特に具体的な顔の変形について仕様を規定していません。

#### リップシンク(enum)

| 名前 | 備考 |
|:-----|:-----|
| aa   | あ   |
| ih   | い   |
| ou   | う   |
| ee   | え   |
| oh   | お   |

#### 瞬き(enum)

| 名前       | 備考             |
|:-----------|:-----------------|
| blink      | 両方の瞼を閉じる |
| blinkLeft  | 左瞼を閉じる     |
| blinkRight | 右瞼を閉じる     |

#### 視線(enum)

| 名前      | 備考                                                                         |
|:----------|:-----------------------------------------------------------------------------|
| lookUp    | ボーンではなくExpressionで視線が動くモデル向け。視線制御を参照してください。 |
| lookDown  | ボーンではなくExpressionで視線が動くモデル向け。視線制御を参照してください。 |
| lookLeft  | ボーンではなくExpressionで視線が動くモデル向け。視線制御を参照してください。 |
| lookRight | ボーンではなくExpressionで視線が動くモデル向け。視線制御を参照してください。 |

#### その他(enum)

| 名前    | 備考                             |
|:--------|:---------------------------------|
| neutral | 後方互換性のために残しています。 |

#### overrideMouth, overrideBlink, overrideLookAt

リップシンク、瞬き、視線 はシステムにより自動で値が生成されることが想定されます。
そのため、 これらの Expression が他の Expression と同時に有効になってしまい、
メッシュが破綻してしまう可能性があります。

例えば、 

* `happy` 中に `aa` が来て口が2倍開いてしまう
* `sad` 中に `blink` が来て目が2回閉じてしまう
* `blink` 中に `lookAt` が来て目がはみ出す

などです。
これらを防御するために、 Expression に対して overrideMouth, overrideBlink, overrideLookAt を設定できます。
(blink に対する overrideBlink のように同種に対する設定は無効です)
設定内容はすべて同じで、効果は下記のとおりです。

| 名前  | 備考                                                                                                                                                                           |
|:------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| none  | 何もしない                                                                                                                                                                     |
| block | 対象の weight を 0 にする。例えば、 happy に overrideBlink=block が設定されているときに、 happy.weight>0 になると blink,blinkLeft,blinkRight の weight を 0 に override する。 |
| blend | 対象の weight を 減衰させる。例えば、 happy に overrideBlink=blend が設定されているときに、 blink,blinkLeft,blinkRight を happy.weight とブレンドして減衰させる(後述)。        |

blend 詳細

例えば、happy が overrideBlink = blend に設定されている場合、
happy の値が 0 から 1 にフェードするに従い、線形に blink を減衰させます。
0~1 の間の中間値の挙動が block と異なります。

```js
// 何らか疑似コードで示す。仮
var value = 0;
if (happyWeight > 0 && happy.overrideBlink == "blend") value += happyWeight;
if (angryWeight > 0 && happy.overrideBlink == "blend") value += angryWeight;
if (sadWeight > 0 && happy.overrideBlink == "blend") value += sadWeight;
if (relaxedWeight > 0 && happy.overrideBlink == "blend") value += relaxedWeight;
if (surprisedWeight > 0 && happy.overrideBlink == "blend") value += surprisedWeight;
var factor = 1.0 - saturate(value);
SetBlinkWeight(blinkWeight * factor);
```

#### MorphTargetBind

`extensions.VRMC_vrm.expressions[*].morphTargetBinds[*]`

Expression と MorphTarget を結びつけます。

| 名前   | 備考                                                               |
|:-------|:-------------------------------------------------------------------|
| node   | 対象node(meshを持っている)のindex                                  |
| index  | 対象morphのindex(すべてのprimitiveが同じmorphTargetを持つ想定です) |
| weight | 適用したときのmorph値                                              |

#### MaterialColorBind

`extensions.VRMC_vrm.expressions[*].materialColorBinds[*]`

Expression と Material の色の変化を結びつけます。

| 名前        | 備考                                             |
|:------------|:-------------------------------------------------|
| material    | 対象のmaterialのindex                            |
| type        | materialの変更対象項目(color, uvScale, uvOffset) |
| targetValue | 適用したときのmaterial値(float4)                 |

`extensions.VRMC_vrm.expressions[*].materialColorBinds[*].type`

それぞれ、以下のパラメータに対応します:

| 名前          | `pbrMetallicRoughness`                 | `KHR_materials_unlit`                  | `VRMC_materials_mtoon`                          |
|:--------------|:---------------------------------------|:---------------------------------------|:------------------------------------------------|
| color         | `pbrMetallicRoughness.baseColorFactor` | `pbrMetallicRoughness.baseColorFactor` | `pbrMetallicRoughness.baseColorFactor`          |
| emissionColor | `emissiveFactor`                       | 未使用                                 | `emissiveFactor`                                |
| shadeColor    | 未使用                                 | 未使用                                 | `extensions.VRMC_materials_mtoon.shadeFactor`   |
| rimColor      | 未使用                                 | 未使用                                 | `extensions.VRMC_materials_mtoon.rimFactor`     |
| outlineColor  | 未使用                                 | 未使用                                 | `extensions.VRMC_materials_mtoon.outlineFactor` |

#### TextureTransformBind

`extensions.VRMC_vrm.expressions[*].textureTransformBinds[*]`

Expression と 対象 Material のテクスチャーの scale, offset の変化を結びつけます。
対象マテリアルで使われるテクスチャのうち、UVアクセスするテクスチャすべてが同じ値を使用することとします。

UVアクセスしないテクスチャは、MToon の `matcap` です。

| 名前     | 備考                                          |
|:---------|:----------------------------------------------|
| material | 対象のmaterialのindex                         |
| scale    | 適用したときのscale値(float2, default=[1, 1]) |
| offset   | 適用したときのoffset値(float2)                |

### Expression 更新のアルゴリズム

#### MorphTarget

* すべての MorphTarget が0の状態にする
* Expression の値(Weight)を積算する `void AccumulateValue(Expression expression, float value)`
* 積算された値を適用する

#### MaterialColor と TextureTransform

* すべての MaterialColor と TextureTransofrm を初期状態にする(0ではなく)
* Expression の値(Weight)を積算する `void AccumulateValue(Exoressuib expression, float value)`
* 積算された値を適用する `Base + (A.Target - Base) * A.Weight + (B.Target - Base) * B.Weight`
  * MaterialColor と TextureTransform は初期値が 0 とは限らないので、初期値との差分を積算します。

## `VRMC_vrm.lookAt` 視線制御(オプション)

VRMは、ヒューマノイド向けに視線制御を定義しています。

| 名前               | 備考                                                                 |
|:-------------------|:---------------------------------------------------------------------|
| lookAtType         | bone または expression                                               |
| offsetFromHeadBone | lookAtの基準位置(両目の間が目安)へのヘッドボーンからの位置offsetです |
| horizontalInner    | 水平内側の目の可動範囲                                               |
| horizontalOuter    | 水平外側の目の可動範囲                                               |
| verticalDown       | 下方向の目の可動範囲                                                 |
| verticalUp         | 上方向の目の可動範囲                                                 |

### LookAtType

| 名前       | 備考                                                               |
|:-----------|:-------------------------------------------------------------------|
| bone       | leftEyeボーンとrightEyeボーンで視線制御します                      |
| expression | Expression のLookAt, LookDown, LookLeft, LookRightで視線制御します |

### 水平内外、垂直上下

目の可動範囲を調整します。

#### 水平内側

`extensions.VRMC_vrm.lookAt.horizontalInner`

* 左目の右方向
* 右目の左方向
* boneタイプ: outputScale には leftEye, rightEye ボーンの Euler 角(radian) による最大回転角度を指定します
* expressionタイプ: outputScale には LookLeft, LookRight expression の最大適用量を指定します(最大1.0)

```
Y = clamp(yaw, 0, horizontalInner.inputMaxValue)/horizontalInner.inputMaxValue * horizontalInner.outputScale 
```

#### 水平外側

`extensions.VRMC_vrm.lookAt.horizontalOuter`

* 左目の左方向
* 右目の右方向
* boneタイプ: outputScale には leftEye, rightEye ボーンの Euler 角(radian) による最大回転角度を指定します
* expressionタイプ: outputScale には LookLeft, LookRight expression の最大適用量を指定します(最大1.0)

```
Y = clamp(yaw, 0, horizontalOuter.inputMaxValue)/horizontalOuter.inputMaxValue * horizontalOuter.outputScale 
```

#### 垂直下方向

`extensions.VRMC_vrm.lookAt.verticalDown`

* 左目の下方向
* 右目の下方向
* boneタイプ: outputScale には leftEye, rightEye ボーンの Euler 角(radian) による最大回転角度を指定します
* expressionタイプ: outputScale には LookLeft, LookRight expression の最大適用量を指定します(最大1.0)

```
Y = clamp(yaw, 0, verticalDown.inputMaxValue)/verticalDown.inputMaxValue * verticalDown.outputScale 
```

#### 垂直上方向

`extensions.VRMC_vrm.lookAt.verticalUp`

* 左目の上方向
* 右目の上方向
* boneタイプ: outputScale には leftEye, rightEye ボーンの Euler 角(radian) による最大回転角度を指定します
* expressionタイプ: outputScale には LookLeft, LookRight Expression の最大適用量を指定します(最大1.0)

```
Y = clamp(yaw, 0, verticalUp.inputMaxValue)/verticalUp.inputMaxValue * verticalUp.outputScale 
```

#### Boneタイプ

可動範囲調整後の Yaw, Pitch 角の値をオイラー角として leftEye, rightEye ボーンの LocalRotation に適用します。

#### Expressionタイプ

可動範囲調整後の Yaw, Pitch 角をExpressionのweight値として LookLeft, LookRight, LookDown, LookUp Expressionに適用します。

### LookAtのアルゴリズム

* HeadBoneのforwardベクトルと、HeadBone + firstPersonBoneOffset の位置から注視点を結んだベクトルが為す方向の Yaw Pitch 角を計算
* x, y = ClampAndScale(yaw, pitch)
* apply(x, y)

#### Boneタイプ

| bone と yaw, pitch              | 備考                                                 |
|:--------------------------------|:-----------------------------------------------------|
| leftEye + yaw(左)               | horizontalOuter を適用してオイラー角として反映します |
| leftEye + yaw(右)               | horizontalInner を適用してオイラー角として反映します |
| rightEye + yaw(左)              | horizontalInner を適用してオイラー角として反映します |
| rightEye + yaw(右)              | horizontalOuter を適用してオイラー角として反映します |
| leftEye or rightEye + pitch(下) | verticalDown を適用してオイラー角として反映します    |
| leftEye or rightEye + pitch(上) | verticalUp を適用してオイラー角として反映します      |

#### Expressionタイプ

| bone と yaw, pitch              | 備考                                                                 |
|:--------------------------------|:---------------------------------------------------------------------|
| leftEye + yaw(左)               | horizontalOuter を適用して Expression LookLeft の値として反映します  |
| leftEye + yaw(右)               | horizontalInner を適用して Expression LookRight の値として反映します |
| rightEye + yaw(左)              | horizontalInner を適用して Expression LookLeft の値として反映します  |
| rightEye + yaw(右)              | horizontalOuter を適用して Expression LookRight の値として反映します |
| leftEye or rightEye + pitch(下) | verticalDown を適用して Expression LookDown の値として反映します     |
| leftEye or rightEye + pitch(上) | verticalUp を適用して Expression LookUp の値として反映します         |

LookAtのExpressionタイプは、 MorphTarget タイプと TextureUVOffset タイプがありますが、ここでの処理は同じです。

## Known Implementations

* https://vrm.dev/vrm_applications/

## Resources

* https://vrm.dev/
