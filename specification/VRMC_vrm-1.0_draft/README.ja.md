# VRMC_vrm

## 目次

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [Overview](#overview)
  - [形式と拡張子](#%E5%BD%A2%E5%BC%8F%E3%81%A8%E6%8B%A1%E5%BC%B5%E5%AD%90)
  - [バージョン](#%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3)
  - [モデルのメタ情報](#%E3%83%A2%E3%83%87%E3%83%AB%E3%81%AE%E3%83%A1%E3%82%BF%E6%83%85%E5%A0%B1)
  - [ヒューマノイド](#%E3%83%92%E3%83%A5%E3%83%BC%E3%83%9E%E3%83%8E%E3%82%A4%E3%83%89)
  - [モデルの正規化](#%E3%83%A2%E3%83%87%E3%83%AB%E3%81%AE%E6%AD%A3%E8%A6%8F%E5%8C%96)
  - [Expression(表情)](#expression%E8%A1%A8%E6%83%85)
  - [一人称](#%E4%B8%80%E4%BA%BA%E7%A7%B0)
  - [視線制御](#%E8%A6%96%E7%B7%9A%E5%88%B6%E5%BE%A1)
  - [Material](#material)
  - [Constraint](#constraint)
  - [更新の適用順](#%E6%9B%B4%E6%96%B0%E3%81%AE%E9%81%A9%E7%94%A8%E9%A0%86)
- [glTF Schema Updates](#gltf-schema-updates)
  - [座標の単位](#%E5%BA%A7%E6%A8%99%E3%81%AE%E5%8D%98%E4%BD%8D)
  - [使わない項目](#%E4%BD%BF%E3%82%8F%E3%81%AA%E3%81%84%E9%A0%85%E7%9B%AE)
  - [各種名前の制約](#%E5%90%84%E7%A8%AE%E5%90%8D%E5%89%8D%E3%81%AE%E5%88%B6%E7%B4%84)
  - [Meshの格納の制約](#mesh%E3%81%AE%E6%A0%BC%E7%B4%8D%E3%81%AE%E5%88%B6%E7%B4%84)
- [JSON Schema](#json-schema)
- [Known Implementations](#known-implementations)
- [Resources](#resources)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

* 進藤哲郎
* 廣瀬 淳一
* 蘇柏彰

## Status

* In development

## Dependencies

Written against the glTF 2.0 spec.

* require VRMC_materials_mtoon extension
* require VRMC_springBone extension
* require KHR_materials_unlit extension
* require KHR_texture_transform extension

## Overview

GLTFシーンにVRアバター向けの人間型モデル一体分の情報を格納します。
VRM拡張に追加情報を保持するとともに既存のGLTF部分にも追加の制約を課すことで、人型モデルをプログラムから統一的に操作できるようにします。

## glTF Schema Updates

### 形式と拡張子

`.glb` 形式で保存し拡張子に `.vrm` を使います。

### VRM

VRM拡張のルートオブジェクトです。

#### プロパティ

| 名前        | 値       | 説明                | 必須   |
|:------------|:---------|:--------------------|:-------|
| specVersion | `string` | VRMの仕様バージョン | ✅ Yes |
| meta        | `object` | VRMのメタ情報       | ✅ Yes |

#### vrm.specVersion ✅

VRMの仕様バージョンを表します。

エクスポーター実装のバージョンは、 `assets.generator` に出力します。

- 型: `string`
- 必須: Yes

#### vrm.meta ✅

VRMの[メタ情報](#meta)です。

- 型: `object`
- 必須: Yes

### Meta

VRMのメタ情報を記述するパラメータ群です。

#### プロパティ

| 名前                           | 値         | 説明                                                           | 必須                            |
|:-------------------------------|:-----------|:---------------------------------------------------------------|:--------------------------------|
| name                           | `string`   | モデルの名前                                                   | ✅ Yes                          |
| version                        | `string`   | モデルのバージョン                                             | No                              |
| authors                        | `string[]` | モデルの作者名                                                 | ✅ Yes                          |
| copyrightInformation           | `string`   | モデルの著作権者                                               | No                              |
| contactInformation             | `string`   | モデルの作者（代表者）への連絡先                               | No                              |
| references                     | `string[]` | モデルの「親作品」となるようなものがあれば、その情報           | No                              |
| thirdPartyLicenses             | `string`   | モデルのサードパーティライセンス表記                           | No                              |
| thumbnailImage                 | `integer`  | モデルのサムネイルとなる画像へのインデックス                   | No                              |
| avatarPermission               | `string`   | このモデルに人格を与えることの許諾範囲                         | No, 初期値: `OnlyAuthor`        |
| allowExcessivelyViolentUsage   | `boolean`  | このモデルの過剰な暴力表現を含むコンテンツでの利用を許可するか | No, 初期値: `false`             |
| allowExcessivelySexualUsage    | `boolean`  | このモデルの過剰な性的表現を含むコンテンツでの利用を許可するか | No, 初期値: `false`             |
| commercialUsage                | `string`   | このモデルを利用した商用利用の許可範囲                         | No, 初期値: `PersonalNonProfit` |
| allowPoliticalOrReligiousUsage | `boolean`  | このモデルの政治・宗教用途での利用を許可するか                 | No, 初期値: `false`             |
| creditNotation                 | `string`   | このモデルのクレジット表記の強制および放棄の指定               | No, 初期値: `required`          |
| allowRedistribution            | `boolean`  | このモデルの再配布を許可するか                                 | No                              |
| modification                   | `string`   | このモデルの改変の許可範囲                                     | No, 初期値: `prohibited`        |
| otherLicenseUrl                | `string`   | その他のライセンス条件があれば、そのURL                        | No                              |

#### meta.name ✅

モデルの名前です。

- 型: `string`
- 必須: Yes

#### meta.version

モデルのバージョンです。

- 型: `string`
- 必須: No

#### meta.authors ✅

モデルの作者名です。

必ず、一つ以上の空文字でない項目が必要です。
アバターの造形者・代表者名を最初に書くことを推奨します。

- 型: `string[]`
- 必須: Yes

#### meta.copyrightInformation

モデルの著作権者です。

このモデルの著作権を表示するために使われることを想定しており、直前の `authors` とは明確に区別して扱います。

- 型: `string`
- 必須: No

#### meta.contactInformation

モデルの作者（代表者）への連絡先です。

- 型: `string`
- 必須: No

#### meta.references

モデルの「親作品」となるようなものがあれば、その情報を記述します。

- 型: `string[]`
- 必須: No

#### meta.thirdPartyLicenses

モデルのサードパーティライセンス表記が必要であれば、ここに記述します。

改行を用いて、複数行に渡る文書を記述可能です。

- 型: `string`
- 必須: No

#### meta.thumbnailImage

モデルのサムネイルを、 `gltf.images` に定義されたインデックスを用いて指定します。

画像は正方形である必要があります。解像度は 1024x1024 を推奨します。

VRMを利用するアプリケーションが、モデルのアイコンとして表示することを想定しています。

- 型: `integer`
- 必須: No
- 最小値: `>= 0`

#### meta.avatarPermission

このモデルをアバターとして操作し演じることを許可するユーザを指定します。

`onlyAuthor` は、モデルの作者のみにアバターとしての操作が許可されることを示します。
`explicitlyLicensedPerson` は、明示的にライセンス許諾されたユーザにアバターとしての操作が許可されることを示します。例えば、有料で販売されるモデルに対して用いられることを想定しています。
`everyone` は、誰にでもこのモデルをアバターとして操作することが許可されることを示します。

- 型: `string`
- 必須: No, 初期値: `onlyAuthor`
- 許可された値:
  - `onlyAuthor`
  - `explicitlyLicensedPerson`
  - `everyone`

#### meta.allowExcessivelyViolentUsage

このモデルを、過度の暴力表現を含むコンテンツに利用することを許可するか否かを指定します。

- 型: `boolean`
- 必須: No, 初期値: `false`

#### meta.allowExcessivelySexualUsage

このモデルを、過度の性的表現を含むコンテンツに利用することを許可するか否かを指定します。

- 型: `boolean`
- 必須: No, 初期値: `false`

#### meta.commercialUsage

このモデルを商用利用することを許可するかを指定します。

`personalNonProfit` は、個人のユーザが非商用目的に限り利用することを許可することを示します。
`personalProfit` は、個人のユーザが商用・非商用のどちらの目的に関わらず利用することを許可することを示します。
法人のユーザは、このプロパティが `corporation` でない限り、該当するモデルの利用が許可されません。

- 型: `string`
- 必須: No, 初期値: `personalNonProfit`
- 許可された値:
  - `personalNonProfit`
  - `personalProfit`
  - `corporation`

#### meta.allowPoliticalOrReligiousUsage

このモデルを、政治的・宗教的なコンテンツに対して利用することを許可するか否かを指定します。

- 型: `boolean`
- 必須: No, 初期値: `false`

#### meta.creditNotation

このモデルのクレジット表記を強制および放棄することを指定します。

このプロパティが `required` の場合、ユーザはモデルのクレジット表記を必ずしなければなりません。
このプロパティが `unnecessary` の場合、ユーザはモデルのクレジット表記を必ずする必要はありません。
`abandoned` は、モデルのクレジット表記を放棄することを指定します。

- 型: `string`
- 必須: No, 初期値: `required`
- 許可された値:
  - `required`
  - `unnecessary`
  - `abandoned`

#### meta.allowRedistribution

このモデルを再配布することを許可するか否かを指定します。

- 型: `boolean`
- 必須: No, 初期値: `false`

#### meta.modification

このモデルを改変することの条件を指定します。

このプロパティが `prohibited` の場合、モデルの改変を許可しないことを示します。
このプロパティが `inherited` もしくは `notInherited` の場合、モデルの改変を許可することを示します。
ユーザがこのプロパティが `inherited` のモデルを改変した場合、改変したモデルは元のモデルのライセンス条件を継承する必要があります。

- 型: `string`
- 必須: No, 初期値: `prohibited`
- 許可された値:
  - `prohibited`
  - `inherited`
  - `notInherited`

#### meta.otherLicenseUrl

その他のライセンス条件があれば、そのURLを指定します。

- 型: `string`
- 必須: No

### ヒューマノイド

`extensions.VRMC_vrm.humanoid`

VRM は、人間型モデルの仕様を定めています。
GLTFのシーン(ノードツリー)の、 `Hips` に該当するノードを頂点とした部分を特にヒューマノイド・スケルトンと呼びます。

#### ヒューマノイド・スケルトン仕様

* すべての必須ボーンが含まれている
* 同じボーンはひとつずつで重複しない
* ボーン同士の親子関係が決まっている
  * `[親ボーンを探す]` ボーンを遡って、下表の親ボーンを探します。最初に見つかったボーンが、
    * 下表の親ボーンであるとき => OK
    * 下表の親ボーンでなく必須ボーンであるとき => Error
    * 下表の親ボーンでなく非必須ボーンであるときは => 探すボーンを非必須ボーンの親に変更して、`[親ボーンを探す]` に戻ります
* ボーンの間にボーンではないノードが入ることは許容します(LowerLegの親がemptyでその親がUpperLegであるなど)
* `向き` は、親ボーンからボーンへの方向です。 `TPose` のときに守ることが推奨される位置関係です
  * アプリケーションでボーンの向きを判定するときにトラブルの原因になりやすいので、親と子のボーンを同じまたは非常に近い座標にすることは非推奨です(浮動小数点で有効な距離を離してください)。

#### ヒューマノイド・ボーン(enum)

##### 胴(enum)

| ボーン名前 | 必須 | 親ボーン   | 向き | 位置の目安 | 備考                                                     |
|:-----------|:-----|:-----------|:-----|------------|----------------------------------------------------------|
| hips       | 必須 |            | Y+   | 股間       | 通常、このボーンだけが移動し、他のボーンは回転だけします |
| spine      | 必須 | hips       | Y+   | 骨盤の上端 |                                                          |
| chest      |      | spine      | Y+   | 胸郭の下端 | 0.X では必須だった                                       |
| upperChest |      | chest      | Y+   |            |                                                          |
| neck       |      | upperChest | Y+   | 首の付け根 | 0.X では必須だった                                       |

* 骨盤に相当するボーンがhips から `Y-` 向きになっている場合がありますが非推奨です(inverted pelvis)。

##### 頭(enum)

| ボーン名前 | 必須 | 親ボーン | 向き | 位置の目安 | 備考                       |
|:-----------|:-----|:---------|------|------------|----------------------------|
| head       | 必須 | neck     |      | 首の上端   |                            |
| leftEye    |      | head     |      |            | 目線をボーンで動かすモデル |
| rightEye   |      | head     |      |            | 目線をボーンで動かすモデル |
| jaw        |      | head     |      |            |                            |

##### 脚(enum)

| ボーン名前    | 必須 | 親ボーン      | 向き | 位置の目安 | 備考 |
|:--------------|:-----|:--------------|------|------------|------|
| leftUpperLeg  | 必須 | hips          | Y-   | 脚の付け根 |      |
| leftLowerLeg  | 必須 | leftUpperLeg  | Y-   | 膝         |      |
| leftFoot      | 必須 | leftLowerLeg  | Y-Z+ | 足首       |      |
| leftToes      |      | leftFoot      |      |            |      |
| rightUpperLeg | 必須 | hips          |      | 脚の付け根 |      |
| rightLowerLeg | 必須 | rightUpperLeg | Y-   | 膝         |      |
| rightFoot     | 必須 | rightLowerLeg | Y-Z+ | 足首       |      |
| rightToes     |      | rightFoot     |      |            |      |

##### 腕(enum)

| ボーン名前    | 必須 | 親ボーン      | 向き | 位置の目安   | 備考 |
|:--------------|:-----|:--------------|------|--------------|------|
| leftShoulder  |      | chest         | X-   |              |      |
| leftUpperArm  | 必須 | leftShoulder  | X-   | 上腕の付け根 |      |
| leftLowerArm  | 必須 | leftUpperArm  | X-   | 肘           |      |
| leftHand      | 必須 | leftLowerArm  | X-   | 手首         |      |
| rightShoulder |      | chest         | X+   |              |      |
| rightUpperArm | 必須 | rightShoulder | X+   | 上腕の付け根 |      |
| rightLowerArm | 必須 | rightUpperArm | X+   | 肘           |      |
| rightHand     | 必須 | rightLowerArm | X+   | 手首         |      |

##### 指(enum)

| ボーン名前              | 必須 | 親ボーン                | 向き | 位置の目安 | 備考 |
|:------------------------|:-----|:------------------------|------|------------|------|
| leftThumbProximal       |      | leftHand                | +X+Z |            |      |
| leftThumbIntermediate   |      | leftThumbProximal       | +X   |            |      |
| leftThumbDistal         |      | leftThumbIntermediate   | +X   |            |      |
| leftIndexProximal       |      | leftHand                | +X   |            |      |
| leftIndexIntermediate   |      | leftIndexProximal       | +X   |            |      |
| leftIndexDistal         |      | leftIndexIntermediate   | +X   |            |      |
| leftMiddleProximal      |      | leftHand                | +X   |            |      |
| leftMiddleIntermediate  |      | leftMiddleProximal      | +X   |            |      |
| leftMiddleDistal        |      | leftMiddleIntermediate  | +X   |            |      |
| leftRingProximal        |      | leftHand                | +X   |            |      |
| leftRingIntermediate    |      | leftRingProximal        | +X   |            |      |
| leftRingDistal          |      | leftRingIntermediate    | +X   |            |      |
| leftLittleProximal      |      | leftHand                | +X   |            |      |
| leftLittleIntermediate  |      | leftLittleProximal      | +X   |            |      |
| leftLittleDistal        |      | leftLittleIntermediate  | +X   |            |      |
| rightThumbProximal      |      | rightHand               | -X   |            |      |
| rightThumbIntermediate  |      | rightThumbProximal      | -X   |            |      |
| rightThumbDistal        |      | rightThumbIntermediate  | -X+Z |            |      |
| rightIndexProximal      |      | rightHand               | -X   |            |      |
| rightIndexIntermediate  |      | rightIndexProximal      | -X   |            |      |
| rightIndexDistal        |      | rightIndexIntermediate  | -X   |            |      |
| rightMiddleProximal     |      | rightHand               | -X   |            |      |
| rightMiddleIntermediate |      | rightMiddleProximal     | -X   |            |      |
| rightMiddleDistal       |      | rightMiddleIntermediate | -X   |            |      |
| rightRingProximal       |      | rightHand               | -X   |            |      |
| rightRingIntermediate   |      | rightRingProximal       | -X   |            |      |
| rightRingDistal         |      | rightRingIntermediate   | -X   |            |      |
| rightLittleProximal     |      | rightHand               | -X   |            |      |
| rightLittleIntermediate |      | rightLittleProximal     | -X   |            |      |
| rightLittleDistal       |      | rightLittleIntermediate | -X   |            |      |

#### Tポーズ仕様

スケルトンは初期姿勢としてTポーズをとっている必要があります。
TPoseの仕様は以下の通りです。

* rootボーン(hipsの祖先でもっとも根元にあるボーン)は原点
* モデルの踵が接地(Y=0)している

| モデルの部分                                                       | 方向 |
|:-------------------------------------------------------------------|:-----|
| 前                                                                 | Z-   |
| 左                                                                 | X+   |
| 上                                                                 | Y+   |
| 胴体(hips - spine - chest - upperChest - upperChest - neck - head) | Y+   |
| 左右脚(upperLeg - lowerLeg)                                        | Y-   |
| 左右足                                                             | Y-Z- |
| 左右つま先                                                         | Z-   |
| 左腕(shoulder - upperArm - lowerArm - hand)と各指                  | X+   |
| 左親指 Proximal                                                    | X+Z- |
| 右腕(shoulder - upperArm - lowerArm - hand)と各指                  | X-   |
| 右親指 Proximal                                                    | X-Z- |
| 手のひら                                                           | Y-   |

### モデルの正規化

VRMモデルのGLTF部分に対する制約と、それを実現するためのアルゴリズムです。

#### ノードの正規化

VRMのノードは以下の制約を受けます。
初期姿勢(T-Pose)で、

* 回転が無い
* スケールが無い(1, 1, 1)

#### メッシュの正規化

ノードの正規化を達成するために、メッシュを正規化する必要があります。
正規化されたメッシュはスキニングしていない状態で以下の制約を受けます

* 初期姿勢のヒューマノイド・スケルトンと重なる(skin.rootがある場合はその座標を加算する)
  * メッシュの前は Z-
  * メッシュの右は X+
  * メッシュの上は Y+

結果として、`skin.inverseBindMatrices` は、回転・スケールを含まずに対象ボーンのワールド座標に対する負の移動のみを含みます。

```js
// Inverse Bind Matrices
// [x, y, z] = bone.translation - skin.translation
[1, 0, 0, 0]
[0, 1, 0, 0]
[0, 0, 1, 0]
[-x,-y,-z, 1]
```

スキニングに関して以下の制約を受けます。

* skin.skeleton が無い、もしくは原点にあるノード

#### 正規化のアルゴリズム

* モデルをT-Poseにする
* skinなし
  * 各頂点にNode.matrixを乗算する
* skinあり
  * BoneWeightの無い頂点がもしあれば、skin.root に対しするBoneWeightを付与する
  * メッシュをスキニングして結果をメッシュとして再取り込みする(bake, freeze)
* Nodeの回転・スケールを除去する

### Expression(表情)

BlendShape は MorphTarget と同じものを指しており意味が異なるため、 vrm-0.0 の BlendShape から Expression に改名しました。
MorphTarget の集合に意味(表情、リップシンク、まばたき、目線等)を与える機能です。
代表して Expression という名前にしています。

VRMは、ヒューマノイド向けに MorphTarget を拡張しています。
複数のMorphTargetをグループ化して意味(瞬き、あいうえお、喜怒哀楽)を持たせます。
また、一部のマテリアル値(color, texture offset+scale)を変化させることができます。

#### ExpressionPreset(enum)

##### 表情(enum)

| 名前    | 備考                                   |
|:--------|:---------------------------------------|
| neutral | 待機状態。`TODO: 廃止して、ベイクする` |
| joy     | 喜                                     |
| angry   | 怒                                     |
| sorrow  | 哀                                     |
| fun     | 楽                                     |

##### リップシンク(enum)

| 名前 | 備考 |
|:-----|:-----|
| aa   | あ   |
| ih   | い   |
| ou   | う   |
| ee   | え   |
| oh   | お   |

##### 瞬き(enum)

| 名前       | 備考             |
|:-----------|:-----------------|
| blink      | 両方の瞼を閉じる |
| blinkLeft  | 左瞼を閉じる     |
| blinkRight | 右瞼を閉じる     |

##### 視線(enum)

| 名前      | 備考                                                                   |
|:----------|:-----------------------------------------------------------------------|
| lookUp    | ボーンではなくExpressionで視線が動くモデル向け。[視線制御](#視線制御)で詳述 |
| lookDown  | ボーンではなくExpressionで視線が動くモデル向け。[視線制御](#視線制御)で詳述 |
| lookLeft  | ボーンではなくExpressionで視線が動くモデル向け。[視線制御](#視線制御)で詳述 |
| lookRight | ボーンではなくExpressionで視線が動くモデル向け。[視線制御](#視線制御)で詳述 |

##### ユーザー定義(enum)

| 名前   | 備考                                                                                    |
|:-------|:----------------------------------------------------------------------------------------|
| custom | Applicationが独自に決めたExpressionを使う場合などに使用します。nameで識別してください。 |

#### Expressionの仕様

`extensions.VRMC_vrm.expressions`

各Expressionを一意に識別するために以下の制約に従ってください

preset が custom 以外の時

* preset 同じものを複数回使わない
* name を 空文字列にする

preset が custom の時

* name 同じものを複数回使わない

| 名前                             | 備考                                                                                                    |
|:---------------------------------|:--------------------------------------------------------------------------------------------------------|
| expressions[*].preset       | 上記のExpressionを一意に識別するために以下の制約に従ってください                                                                                       |
| expressions[*].name         | 上記のExpressionを一意に識別するために以下の制約に従ってください                                                          |
| expressions[*].is_binary    | trueの場合 value!=0 を 1 とみなします                                                                        |
| expressions[*].morphTargetBinds| MorphTargetBind(後述) のリスト                                                                            |
| expressions[*].materialColorBinds| MaterialValueBind(後述) のリスト                                                                        |
| expressions[*].textureTransformBinds| TextureTransformBind(後述) のリスト                                                                  |
| expressions[*].ignoreBlink  | このExpressionのWeightが0でないときに、blink, blink_L, blink_R のウェイトを強制的に0にします。               |
| expressions[*].ignoreLookAt | このExpressionのWeightが0でないときに、lookUp, lookDown, lookLeft, lookRight のウェイトを強制的に0にします。 |
| expressions[*].ignoreMouth  | このExpressionのWeightが0でないときに、A, I, U, E, O のウェイトを強制的に0にします。                         |

##### MorphTargetBind

`extensions.VRMC_vrm.expressions[*].morphTargetBinds[*]`

Expression と MorphTarget を結びつけます。

| 名前   | 備考                                                                                          |
|:-------|:----------------------------------------------------------------------------------------------|
| node   | 対象node(meshを持っている)のindex                                                             |
| index  | 対象morphのindex(すべてのprimitiveが同じmorphを持ちます[Meshの格納の制約](#Meshの格納の制約)) |
| weight | 適用したときのmorph値                                                                         |

##### MaterialColorBind

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

##### TextureTransformBind

`extensions.VRMC_vrm.expressions[*].textureTransformBinds[*]`

Expression と 対象 Material のテクスチャーの scale, offset の変化を結びつけます。
対象マテリアルで使われるテクスチャのうち、UVアクセスするテクスチャすべてが同じ値を使用することとします。

| 名前        | 備考                                             |
|:------------|:-------------------------------------------------|
| material    | 対象のmaterialのindex                            |
| scale       | 適用したときのscale値(float2, default=[1, 1])    |
| offset      | 適用したときのoffset値(float2)                   |

#### Expression 更新のアルゴリズム

##### Expression の識別

* preset が custom 以外の場合は、presetで識別(custom 以外は preset が unique)
* custom の場合は、name で識別(custom は name が unique)

##### MorphTarget

* すべての MorphTarget が0の状態にする
* Expression の値(Weight)を積算する `void AccumulateValue(Expression expression, float value)`
* 積算された値を適用する

##### MaterialColor と TextureTransform

* すべての MaterialColor と TextureTransofrm を初期状態にする(0ではなく)
* Expression の値(Weight)を積算する `void AccumulateValue(Exoressuib expression, float value)`
* 積算された値を適用する `Base + (A.Target - Base) * A.Weight + (B.Target - Base) * B.Weight`
  * MaterialColor と TextureTransform は初期値が 0 とは限らないので、初期値との差分を積算します。

### 一人称

VRMは、VRを想定した一人称視点の設定を定義しています。
lookAt.offsetFromHeadBone を HMDの参考位置に利用できます。

#### MeshAnnotation

Mesh単位での描画を制御します。

`extensions.VRMC_vrm.firstPerson.meshAnnotations[*]`

| 名前            | 備考                                |
|:----------------|:------------------------------------|
| node            | 対象node(meshを持っている)へのindex |
| firstPersonFlag | 後述                                |

firstPersonFlag。VRアプリでモデルを使用した場合に、自モデルの描画をHMDとそれ以外で切り分けます。

#### MeshAnnotation(enum)

`thirdPersonOnly` は、VR描画時に自分の頭部を非表示にすることを意図しています。VR視点カメラは、頭部の中に位置することが想定されます。そのため、顔・頭・髪のポリゴンが視界に見えてしまうのを防ぎます。

特に描き分ける必要ないオブジェクトは、 `both` にして、すべてのカメラから描画されるようにします。

`firstPersonOnly` は、自分だけに見えて他人視点からは見えない設定です。アプリ側で特別な用途が無ければ使いません。

`auto` はMeshを頭部(`thirdPersonOnly`)とその他(`both`)に分割してください。次の節で詳述します。

| 名前            | 1人称カメラ(VR視点) | その他のカメラ | 例                                                                 |
|:----------------|:--------------------|:---------------|--------------------------------------------------------------------|
| thirdPersonOnly | 描画しない          | 描画する       | 顔・目・頭・髪、帽子・ヘルメットなどVR視界を遮るもの               |
| firstPersonOnly | 描画する            | 描画しない     | プレイヤーだけに見えてたの視点からは見えないユーザーインタフェース |
| both            | 描画する            | 描画する       |                                                                    |

#### MeshAnnotation.Autoのアルゴリズム

* Meshの頂点をすべて調べて、Headボーンとその子孫のボーンに対するWeightを持つ頂点を集める
* 上記の頂点を含む三角形と含まない三角形に２分したMeshを作成する
* 上記の頂点を含むMeshをThirdPersonOnly、含まないMeshをBothとしたに設定する

### 視線制御

`extensions.VRMC_vrm.lookAt`

VRMは、ヒューマノイド向けに視線制御を定義しています。

| 名前               | 備考                                                                 |
|:-------------------|:---------------------------------------------------------------------|
| lookAtType         | bone または expression                                               |
| offsetFromHeadBone | lookAtの基準位置(両目の間が目安)へのヘッドボーンからの位置offsetです |
| horizontalInner    | 水平内側の目の可動範囲                                               |
| horizontalOuter    | 水平外側の目の可動範囲                                               |
| verticalDown       | 下方向の目の可動範囲                                                 |
| verticalUp         | 上方向の目の可動範囲                                                 |

#### LookAtType

| 名前            | 備考                                                              |
|:----------------|:------------------------------------------------------------------|
| bone            | leftEyeボーンとrightEyeボーンで視線制御します                     |
| expression      | Expression のLookAt, LookDown, LookLeft, LookRightで視線制御します |

#### 水平内外、垂直上下

目の可動範囲を調整します。

##### 水平内側

`extensions.VRMC_vrm.lookAt.horizontalInner`

* 左目の右方向
* 右目の左方向
* boneタイプ: outputScale には leftEye, rightEye ボーンの Euler 角(radian) による最大回転角度を指定します
* expressionタイプ: outputScale には LookLeft, LookRight expression の最大適用量を指定します(最大1.0)

```
Y = clamp(yaw, 0, horizontalInner.inputMaxValue)/horizontalInner.inputMaxValue * horizontalInner.outputScale 
```

##### 水平外側

`extensions.VRMC_vrm.lookAt.horizontalOuter`

* 左目の左方向
* 右目の右方向
* boneタイプ: outputScale には leftEye, rightEye ボーンの Euler 角(radian) による最大回転角度を指定します
* expressionタイプ: outputScale には LookLeft, LookRight expression の最大適用量を指定します(最大1.0)

```
Y = clamp(yaw, 0, horizontalOuter.inputMaxValue)/horizontalOuter.inputMaxValue * horizontalOuter.outputScale 
```

##### 垂直下方向

`extensions.VRMC_vrm.lookAt.verticalDown`

* 左目の下方向
* 右目の下方向
* boneタイプ: outputScale には leftEye, rightEye ボーンの Euler 角(radian) による最大回転角度を指定します
* expressionタイプ: outputScale には LookLeft, LookRight expression の最大適用量を指定します(最大1.0)

```
Y = clamp(yaw, 0, verticalDown.inputMaxValue)/verticalDown.inputMaxValue * verticalDown.outputScale 
```

##### 垂直上方向

`extensions.VRMC_vrm.lookAt.verticalUp`

* 左目の上方向
* 右目の上方向
* boneタイプ: outputScale には leftEye, rightEye ボーンの Euler 角(radian) による最大回転角度を指定します
* expressionタイプ: outputScale には LookLeft, LookRight Expression の最大適用量を指定します(最大1.0)

```
Y = clamp(yaw, 0, verticalUp.inputMaxValue)/verticalUp.inputMaxValue * verticalUp.outputScale 
```

##### Boneタイプ

可動範囲調整後の Yaw, Pitch 角の値をオイラー角として leftEye, rightEye ボーンの LocalRotation に適用します。

##### Expressionタイプ

可動範囲調整後の Yaw, Pitch 角をExpressionのweight値として LookLeft, LookRight, LookDown, LookUp Expressionに適用します。

#### LookAtのアルゴリズム

* HeadBoneのforwardベクトルと、HeadBone + firstPersonBoneOffset の位置から注視点を結んだベクトルが為す方向の Yaw Pitch 角を計算
* x, y = ClampAndScale(yaw, pitch)
* apply(x, y)

##### Boneタイプ

| bone と yaw, pitch              | 備考                                                 |
|:--------------------------------|:-----------------------------------------------------|
| leftEye + yaw(左)               | horizontalOuter を適用してオイラー角として反映します |
| leftEye + yaw(右)               | horizontalInner を適用してオイラー角として反映します |
| rightEye + yaw(左)              | horizontalInner を適用してオイラー角として反映します |
| rightEye + yaw(右)              | horizontalOuter を適用してオイラー角として反映します |
| leftEye or rightEye + pitch(下) | verticalDown を適用してオイラー角として反映します    |
| leftEye or rightEye + pitch(上) | verticalUp を適用してオイラー角として反映します      |

##### Expressionタイプ

| bone と yaw, pitch              | 備考                                                                 |
|:--------------------------------|:---------------------------------------------------------------------|
| leftEye + yaw(左)               | horizontalOuter を適用して Expression LookLeft の値として反映します  |
| leftEye + yaw(右)               | horizontalInner を適用して Expression LookRight の値として反映します |
| rightEye + yaw(左)              | horizontalInner を適用して Expression LookLeft の値として反映します  |
| rightEye + yaw(右)              | horizontalOuter を適用して Expression LookRight の値として反映します |
| leftEye or rightEye + pitch(下) | verticalDown を適用して Expression LookDown の値として反映します     |
| leftEye or rightEye + pitch(上) | verticalUp を適用して Expression LookUp の値として反映します         |

LookAtのExpressionタイプは、 MorphTarget タイプと TextureUVOffset タイプがありますが、ここでの処理は同じです。

### Material

依存する拡張

#### VRMC_materials_mtoon extension

トゥーンシェーダーを定義しています。

#### KHR_materials_unlit extension

アンライトが必須です。

#### KHR_texture_transform

限定的に対応します。

`materials[*].pbrMetallicRoughness.baseColorTexture.extensions.KHR_texture_transform`

を使ってください。
`uv` アクセスする他のテクスチャもこの値を参照することとします。
Expression の TextureTransformBind との兼ね合いです。

### Constraint

TODO:

### 更新の適用順 

推奨される更新の適用順です。

1. ヒューマノイド・ボーンを解決
2. 頭の位置が決まるのでLookAtを解決
  * Bone型
  * Expression型
3. ブレンドシェイプUpdate
  * LipSync
  * AutoBlink
  * Expression型のLookAt
  * コントローラーなど外部入力
4. ブレンドシェイプApplyする
5. コンストレイントを解決
6. SpringBoneを解決

## glTF Schema Updates

### 座標の単位

glTFの [coordinate-system-and-units](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#coordinate-system-and-units) に準拠してメートル単位です。

### 使わない項目

以下の項目を使用しません。

* animations
* cameras

### 各種名前の制約

* ユニークにする

### Meshの格納の制約

#### TANGENTを保存しない

* TANGENT を保存しない。NormalMapが存在する場合は、MikkTSpaceアルゴリズムで計算すること
* meshes[*].primitives[*].attributes.TANGENT
* meshes[*].primitives[*].targets.TANGENT

#### VertexBufferの制約

ある meshの primitive 毎に異なるバッファ構成を禁止しています。

* あるmeshの各 primitive は同じ attributes を持たなければならない

primitive 毎に独立した morph を禁止します。
primitive ではなく mesh に対して morph targets を設定することを想定しています。

* 各 primitive は同じ targets を持たなければならない
* 各 primitive.targets は同じ attributes を持たなければならない

関連して morph target の名前を

* meshes[*].extras.targetNames (meshes[*].primitives[*].extras.targetNames から変更)

に格納します。各 primitive が同じ morph targets を保持することを前提にしています。

#### VertexBufferの格納方式

```
primitives[*].attributes(各primitiveで独自のaccessorを使う)
 +-----------+ +----------+ +----------+
 |(0)        | |(3)       | |(6)       |POSITION
 +-----------+ +----------+ +----------+
 +-----------+ +----------+ +----------+
 |(1)        | |(4)       | |(7)       |NORMAL
 +-----------+ +----------+ +----------+
 +-----------+ +----------+ +----------+
 |(2)        | |(5)       | |(8)       |TEXCOORD_0
 +-----------+ +----------+ +----------+
      ^         ^            ^
      |         |            |
primitives
  [0].indices   [1].indices  [2].indices
 +-----------+ +----------+ +----------+
 |(9)        | |(10)      | |(11)      |indices
 +-----------+ +----------+ +----------+

box: accessor
```

* すべての primitive が同じattributesを持つようにする
  * 例: `primitive[0]`(POSITION, NORMAL, TEXCOORD_0)に対し `primitive[1]`(POSITION) と構成が違う可能性がある
* primitive.targets も同様

```
NG

primitives[*].attributes(各primitiveで独自のaccessorを使う)
 +-----------+ +----------+ +----------+
 |(0)        | |(3)       | |(6)       |POSITION
 +-----------+ +----------+ +----------+
 +-----------+ +----------+             
 |(1)        | |(4)       |             NORMAL
 +-----------+ +----------+             
 +-----------+                          
 |(2)        |                          TEXCOORD_0
 +-----------+                          
      ^         ^            ^
      |         |            |
primitives
  [0].indices   [1].indices  [2].indices
 +-----------+ +----------+ +----------+
 |(9)        | |(10)      | |(11)      |indices
 +-----------+ +----------+ +----------+

box: accessor
```

## JSON Schema

```js
{
  "extensionsUsed": {
    "VRMC_vrm"
  },
  "extensions": {
    "VRMC_vrm": {
      // VRMの拡張情報
      "specVersion": "1.0"
    }
  },
  // 通常のGLTF-2.0の情報
}
```

* https://github.com/vrm-c/vrm-specification/tree/master/specification/VRMC_vrm-1.0_draft/schema

GLTF-2.0のJsonSchema

* https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/schema

## Known Implementations

https://vrm.dev/vrm_applications/

## Resources

* https://vrm.dev/
