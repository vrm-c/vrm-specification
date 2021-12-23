# VRMC_vrm

*Version 1.0-beta*

## 目次

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [併用する拡張](#%E4%BD%B5%E7%94%A8%E3%81%99%E3%82%8B%E6%8B%A1%E5%BC%B5)
- [KHR_texture_transform の制限](#khr_texture_transform-%E3%81%AE%E5%88%B6%E9%99%90)
  - [VRM1での KHR_texture_transform の非推奨の機能](#vrm1%E3%81%A7%E3%81%AE-khr_texture_transform-%E3%81%AE%E9%9D%9E%E6%8E%A8%E5%A5%A8%E3%81%AE%E6%A9%9F%E8%83%BD)
- [Overview](#overview)
  - [JSON Schema](#json-schema)
  - [VRMC_vrm の仕様バージョン](#vrmc_vrm-%E3%81%AE%E4%BB%95%E6%A7%98%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3)
  - [形式と拡張子](#%E5%BD%A2%E5%BC%8F%E3%81%A8%E6%8B%A1%E5%BC%B5%E5%AD%90)
- [glTF Schema Updates](#gltf-schema-updates)
  - [座標の単位](#%E5%BA%A7%E6%A8%99%E3%81%AE%E5%8D%98%E4%BD%8D)
  - [使わない項目](#%E4%BD%BF%E3%82%8F%E3%81%AA%E3%81%84%E9%A0%85%E7%9B%AE)
  - [保存された TANGENT を無視してもよい](#%E4%BF%9D%E5%AD%98%E3%81%95%E3%82%8C%E3%81%9F-tangent-%E3%82%92%E7%84%A1%E8%A6%96%E3%81%97%E3%81%A6%E3%82%82%E3%82%88%E3%81%84)
    - [`meshes[*].primitives[*].attributes.TANGENT`](#meshesprimitivesattributestangent)
    - [`meshes[*].primitives[*].targets.TANGENT`](#meshesprimitivestargetstangent)
  - [`meshes[*].extras.targetNames` モーフターゲットの名前(推奨)](#meshesextrastargetnames-%E3%83%A2%E3%83%BC%E3%83%95%E3%82%BF%E3%83%BC%E3%82%B2%E3%83%83%E3%83%88%E3%81%AE%E5%90%8D%E5%89%8D%E6%8E%A8%E5%A5%A8)
- [`VRMC_vrm.humanoid` ノードへのヒューマノイドボーンの割り当て(必須)](#vrmc_vrmhumanoid-%E3%83%8E%E3%83%BC%E3%83%89%E3%81%B8%E3%81%AE%E3%83%92%E3%83%A5%E3%83%BC%E3%83%9E%E3%83%8E%E3%82%A4%E3%83%89%E3%83%9C%E3%83%BC%E3%83%B3%E3%81%AE%E5%89%B2%E3%82%8A%E5%BD%93%E3%81%A6%E5%BF%85%E9%A0%88)
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

## 併用する拡張

VRMC_vrm 拡張は、これらの拡張と併用して使用すること想定しています。

* KHR_materials_unlit
* KHR_texture_transform
* VRMC_materials_mtoon
* VRMC_materials_hdr_emissiveMultiplier
* VRMC_springBone
* VRMC_node_constraint

## KHR_texture_transform の制限

(KHR_texture_transform)[https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_texture_transform/README.md] は、
すべてのマテリアルのテクスチャー [https://github.com/KhronosGroup/glTF/blob/master/specification/2.0/schema/textureInfo.schema.json] に対して、
個別に `offset`, `rotation`, `scale`, `texCoord` を指定することができます。

glTF 標準のPBRマテリアルの場合、

* pbrMetallicRoughness.baseColorTexture(KHR_materials_unlit の場合はこれのみ)
* pbrMetallicRoughness.metallicRoughnessTexture
* normalTexture
* occlusionTexture
* emissiveTexture

です。

### VRM1での KHR_texture_transform の非推奨の機能

実装によっては KHR_texture_transform の拡張する項目が個別に設定できないことがあります。
そのため、下記の項目については使用しないことを推奨しています。

* rotation
* texCoord

## Overview

glTF はシーンを表現するのに対して、
VRMはVRアバター向けの人間型モデル一体を表現します。

### モデル空間

VRMでは、VRMモデルを構成するglTFシーンの原点から相対にトランスフォームを観測する「モデル空間」を定義します。
これは、VRMモデルを扱うアプリケーション上のワールド空間とは区別されます。

モデル空間は、 [`VRMC_node_constraint`](../VRMC_node_constraint-1.0_draft/README.ja.md) 拡張で利用されます。

> VRMモデルをアプリケーション上で動かす際は、Humanoidで定義するHipsを動かすだけでなく、
> glTFシーンのルートごとモデルを動かすことにより、モデル空間を尊重することが期待されます。
> 言い換えれば、モデルのルートが常にワールド空間のルートに留まるような利用は推奨されません。

### JSON Schema

```js
{
  "extensionsUsed": {
    "VRMC_vrm"
  },
  "extensions": {
    "VRMC_vrm": {
      // VRM extension
      "specVersion": "1.0-beta",
      "humanoid": {},
      "meta": {},
      "firstPerson": {},
      "expression": {},
      "lookAt": {},
    },
    "VRMC_springBone": {},
    "VRMC_node_constraint": {}
  },
  // glTF-2.0
  "materials": [
    "extensions": {
      "VMRC_materials_mtoon": {},
      "VRMC_materials_hdr_emissiveMultiplier": {}
    }
  ],
}
```

* https://github.com/vrm-c/vrm-specification/tree/master/specification/VRMC_vrm-1.0_draft/schema

GLTF-2.0のJsonSchema

* https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/schema

### VRMC_vrm の仕様バージョン

```json
extensions.VRMC_vrm.specVersion  = "1.0-beta"
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

TANGENT を正しく扱うことが技術的に困難なため、書き出しはしない、読み込みはせずに計算することを許容します。

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
