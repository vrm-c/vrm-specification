# VRMC_vrm_animation

*Version 1.0-draft*

## Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Status](#status)
- [Dependencies](#dependencies)
- [Overview](#overview)
- [Concepts](#concepts)
  - [モデル空間](#%E3%83%A2%E3%83%87%E3%83%AB%E7%A9%BA%E9%96%93)
  - [Animations](#animations)
  - [Humanoid](#humanoid)
  - [Expressions](#expressions)
  - [LookAt](#lookat)
- [glTF Schema Updates](#gltf-schema-updates)
  - [VRMC_vrm_animation](#vrmc_vrm_animation)
    - [Properties](#properties)
    - [JSON Schema](#json-schema)
    - [VRMC_vrm_animation.specVersion ✅](#vrmc_vrm_animationspecversion-)
    - [VRMC_vrm_animation.humanoid ✅](#vrmc_vrm_animationhumanoid-)
    - [VRMC_vrm_animation.expressions](#vrmc_vrm_animationexpressions)
    - [VRMC_vrm_animation.lookAt](#vrmc_vrm_animationlookat)
  - [humanoid](#humanoid)
    - [Properties](#properties-1)
    - [JSON Schema](#json-schema-1)
    - [humanoid.humanBones ✅](#humanoidhumanbones-)
  - [humanoid.humanBones](#humanoidhumanbones)
    - [Properties](#properties-2)
    - [JSON Schema](#json-schema-2)
    - [humanoid.humanBones.(Humanoidボーン名)](#humanoidhumanboneshumanoid%E3%83%9C%E3%83%BC%E3%83%B3%E5%90%8D)
  - [humanoid.humanBones.humanBone](#humanoidhumanboneshumanbone)
    - [Properties](#properties-3)
    - [JSON Schema](#json-schema-3)
    - [humanoid.humanBones.humanBone.node ✅](#humanoidhumanboneshumanbonenode-)
  - [expressions](#expressions)
    - [Properties](#properties-4)
    - [JSON Schema](#json-schema-4)
    - [expressions.preset](#expressionspreset)
    - [expressions.custom](#expressionscustom)
  - [expressions.preset](#expressionspreset-1)
    - [Properties](#properties-5)
    - [JSON Schema](#json-schema-5)
    - [expressions.preset.(プリセット表情名)](#expressionspreset%E3%83%97%E3%83%AA%E3%82%BB%E3%83%83%E3%83%88%E8%A1%A8%E6%83%85%E5%90%8D)
  - [expressions.custom](#expressionscustom-1)
    - [Properties](#properties-6)
    - [JSON Schema](#json-schema-6)
    - [expressions.custom.(プリセット表情名)](#expressionscustom%E3%83%97%E3%83%AA%E3%82%BB%E3%83%83%E3%83%88%E8%A1%A8%E6%83%85%E5%90%8D)
  - [expressions.expression](#expressionsexpression)
    - [Properties](#properties-7)
    - [JSON Schema](#json-schema-7)
    - [expressions.expression.node ✅](#expressionsexpressionnode-)
  - [lookAt](#lookat)
    - [Properties](#properties-8)
    - [JSON Schema](#json-schema-8)
    - [lookAt.node ✅](#lookatnode-)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Status

Draft

## Dependencies

glTF 2.0仕様に向けて策定されています。

また、仕様内の一部定義が [`VRMC_vrm`](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/README.ja.md) に依存します。

## Overview

`VRMC_vrm_animation` 仕様は、人間型モデルに対するアニメーションを記述するためのglTF拡張です。
人間型モデルを記述するglTF拡張である [`VRMC_vrm`](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/README.ja.md) で表現されたモデルに対して適用することを想定しています。

glTFで定義されたノードのヒエラルキーに対して、各ノードと人間のボーン・表情・注視点を関連付けて、任意の人間型モデルに適用できるアニメーションを表現します。
アニメーションの実データは、glTFのコア定義のアニメーション部分を利用します。

アニメーションのみを記述する独立したglTFファイルに使用されることを想定しています。
`VRMC_vrm` を用いて記述されたVRMモデルに対して本拡張が含まれることは想定していません。

## Concepts

### モデル空間

VRMC_vrm_animationの定義を行う上で、「モデル空間」という概念を導入します。
モデル空間とは、モデルを構成するシーンの原点から相対にトランスフォームを観測する空間を指します。
これは、モデルを扱うアプリケーション上のワールド空間とは区別されます。

### Animations

glTFのコア定義のアニメーションを利用します。

原則、アニメーションを用いる際は、 `animations` に定義された最初のアニメーションを読み込むものとします。
1つのファイルに複数のアニメーションが含まれている場合があります。
実装は、1のファイルに含まれた複数のアニメーションに対応することができますが、必須ではありません。

> TODO: 複数アニメーションを許容しますか？1ファイルに複数のアニメーションを持てるようにすると、いちいちヒエラルキーの情報を定義し直す必要がないため、容量面では有利な使い方ができると思います。一方で、取り回しが面倒・混乱を招く、と言ったリスクを鑑みて、最初から1アニメーションにしておく選択もあり得ます。

glTF アニメーションは、任意時間にキーフレームを挿入できるのでフレームレートの概念がありません。
以下の点を考慮し、高すぎず低すぎないフレームレートの目安として秒間 30 フレームを提案します。

* ゲームでは 60 FPS や 30 FPS が一般的です
* モーションキャプチャーの生データ(加工前)は高フレームレートの場合があります
* 映像では 24FPS などがあります

アニメーションを Linear で Interpolation することを想定して、
30 FPS で十分な滑らかさを確保できると思われます。

### Humanoid

Humanoidは、本拡張内で定義する人間のボーンの定義です。
各Humanoidボーンは、glTFのノードとして表現されます。

`VRMC_vrm_animation/humanoid` 内で、glTFのノードと人間のボーンの対応関係を示します。
Humanoidで利用するボーンおよびその構造について、 `VRMC_vrm` 拡張のHumanoidに準拠します。

https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/humanoid.ja.md

ただし、Humanoidボーンのうち `leftEye` ・ `rightEye` は、これらに対してアニメーションデータを持つことはできません。
これらは、後述するLookAtで扱われます。

> 将来的な拡張性を見越して、Humanoidボーンのヒエラルキー全体をアニメーションデータ内に収録しています。

Humanoidボーンとして指定されたノードは、アニメーションが適用されていないレストポーズにおいて、「VRM T-pose」と呼ばれるポーズに従っている必要があります。

https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/tpose.ja.md

Humanoidボーンとして指定されたノードに対するアニメーションを、アニメーションデータとして扱います。
Humanoidボーンに対するアニメーションにスケールを含めてはいけません。
また、Hipsボーン以外に対するアニメーションに平行移動を含めてはいけません。

> 実装内では、例えばUnityのHumanoidシステムを用いて高度なアニメーション転送がされることが望ましいですが、これが難しい場合は、単純にFKアニメーションとして転送することもできます。

> TODO: Hipsより親にアニメーションが含まれていた場合はどうしましょう？
> Rootを指定させた方が良いでしょうか？

### Expressions

Expressionsは、本拡張内で定義する人間の表情の定義です。
各Expressionsは、 [0, 1] の範囲のスカラをウェイトとして持ち、この数値がどの程度その表情が発現しているかを表します。

> `VRMC_vrm` 拡張では、Expressionsのウェイトがメッシュのモーフ・マテリアルの色・テクスチャのUVに対して適用されます。

`VRMC_vrm_animation/expressions` 内で、glTFのノードと表情との対応関係を示します。
Expressionsの定義について、 `VRMC_vrm` 拡張のExpressionsに準拠します。

https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/expressions.ja.md

ただし、プリセット表情のうち `lookUp` ・ `lookDown` ・ `lookLeft` ・ `lookRight` は、これらに対してアニメーションデータを持つことはできません。
これらは、後述するLookAtで扱われます。

Expressionsのうち、 `VRMC_vrm` 拡張でプリセット表情として定義されているものをプリセット表情、それ以外をカスタム表情と定義します。
カスタム表情に、プリセットと同じ名前の表情を含めることはできません。

Expressionsとして指定されたノードに対するアニメーションのうち、平行移動のX成分を、その表情のウェイトに対するアニメーションデータとして扱います。
ウェイト値は [0, 1] の範囲内に収まっていることが望ましいです。
実装は、 [0, 1] の範囲から外れたウェイト値を扱う場合、その値を [0, 1] の範囲内に収めて扱わなければいけません。

> TODO: ノードのTranslationを用いる代わりに、 `animations/i/channels/j/target` 内で `path` が `weights` のアニメーションとして定義し、同 `target` 内で拡張としてExpressionsを定義することも可能です。どちらのほうがよりValidな定義となるでしょうか？

### LookAt

LookAtは、本拡張内で定義する人間の視線の定義です。
LookAtは、一つの注視点を持ち、モデルがその方向に視線を動かすことを示します。

> `VRMC_vrm` 拡張において、LookAtによる視線制御は、ボーンアニメーション

`VRMC_vrm_animation/lookAt` 内で、glTFのノードと注視点との対応関係を示します。

注視点として指定されたノードのモデル空間における位置を、注視点のアニメーションデータとして扱います。

## glTF Schema Updates

`VRMC_vrm_animation` 拡張はglTFのルートレベルに定義されます。

```json
{
  "nodes": [
    // ...
  ],
  "animations": [
    // ...
  ],
  "extensions": {
    "VRMC_vrm_animation": {
      "specVersion": "1.0-draft",
      "humanoid": {
        "humanBones": {
          "hips": { "node": 0 },
          "spine": { "node": 1 },
          "chest": { "node": 2 },
          // ...
        }
      },
      "expressions": {
        "preset": {
          "aa": { "node": 59 },
          "blinkLeft": { "node": 60 },
          "blink": { "node": 61 },
          "happy": { "node": 62 },
          "relaxed": { "node": 63 }
        }
      },
      "lookAt": {
        "node": 64
      }
    }
  },
  // ...
}
```

### VRMC_vrm_animation

本拡張のルートオブジェクトです。

#### Properties

||型|説明|必須|
|:-|:-|:-|:-|
|`specVersion`|`string`|本拡張の仕様バージョン|✅ Yes|
|`humanoid`|`humanoid`|Humanoidボーンに関する定義|No|
|`expressions`|`expressions`|Expressionsの表情とノードの対応関係|No|
|`lookAt`|`lookAt`|LookAtの注視点とノードの対応関係|No|

#### JSON Schema

[VRMC_vrm_animation.schema.json](schema/VRMC_vrm_animation.schema.json)

#### VRMC_vrm_animation.specVersion ✅

`VRMC_vrm_animation` 拡張の仕様バージョンを表します。値は `"1.0-draft"` です。

- 型: `string`
- 必須: Yes

#### VRMC_vrm_animation.humanoid

Humanoidボーンとノードの対応関係を表すオブジェクトです。

- 型: `humanoid`
- 必須: No

#### VRMC_vrm_animation.expressions

Expressionsの表情とノードの対応関係を表すオブジェクトです。

- 型: `expressions`
- 必須: No

#### VRMC_vrm_animation.lookAt

LookAtの注視点とノードの対応関係を表すオブジェクトです。

- 型: `lookAt`
- 必須: No

---

### humanoid

Humanoidボーンに関する定義を行うオブジェクトです。

#### Properties

||型|説明|必須|
|:-|:-|:-|:-|
|humanBones|`humanoid.humanBones`|Humanoidボーンとノードの対応関係を表す|Yes ✅|

#### JSON Schema

[VRMC_vrm_animation.humanoid.schema.json](schema/VRMC_vrm_animation.humanoid.schema.json)

#### humanoid.humanBones ✅

Humanoidボーンとノードの対応関係を表すオブジェクトです。

- 型: `humanoid.humanBones`
- 必須: Yes

### humanoid.humanBones

Humanoidボーンとノードの対応関係を表すオブジェクトです。

#### Properties

||型|説明|必須|
|:-|:-|:-|:-|
|(Humanoidボーン名)|`humanoid.humanBones.humanBone`|ひとつのHumanoidボーン|Mixed|

#### JSON Schema

[VRMC_vrm_animation.humanoid.humanBones.schema.json](schema/VRMC_vrm_animation.humanoid.humanBones.schema.json)

#### humanoid.humanBones.(Humanoidボーン名)

ひとつのHumanoidボーンを表すオブジェクトです。
`VRMC_vrm` 拡張で定義されているHumanoidボーンの名前をキー名として持ちます（`hips` や `leftUpperArm` など）。
ただし、Humanoidボーンのうち `leftEye` ・ `rightEye` は定義できません。

- 型: `humanoid.humanBones.humanBone`
- 必須: No

### humanoid.humanBones.humanBone

ひとつのHumanoidボーンを表すオブジェクトです。

#### Properties

||型|説明|必須|
|:-|:-|:-|:-|
|`node`|`integer`|Humanoidボーンに対応するノードのインデックス|✅ Yes|

#### JSON Schema

[VRMC_vrm_animation.humanoid.humanBones.humanBone.schema.json](schema/VRMC_vrm_animation.humanoid.humanBones.humanBone.schema.json)

#### humanoid.humanBones.humanBone.node ✅

Humanoidボーンに対応するノードのインデックスです。

- 型: `integer`
- 必須: Yes

---

### expressions

Expressionsの表情とノードの対応関係を表すオブジェクトです。

#### Properties

||型|説明|必須|
|:-|:-|:-|:-|
|`preset`|`expressions.preset`|プリセット表情に対する定義|No|
|`custom`|`expressions.custom`|カスタム表情に対する定義|No|

#### JSON Schema

[VRMC_vrm_animation.expressions.schema.json](schema/VRMC_vrm_animation.expressions.schema.json)

#### expressions.preset

プリセット表情に対する定義を含むオブジェクトです。

- 型: `expressions.preset`
- 必須: No


#### expressions.custom

カスタム表情に対する定義を含むオブジェクトです。

- 型: `expressions.custom`
- 必須: No

### expressions.preset

プリセット表情に対する定義を含むオブジェクトです。

#### Properties

||型|説明|必須|
|:-|:-|:-|:-|
|(プリセット表情名)|`expressions.expression`|ひとつのプリセット表情|No|

#### JSON Schema

[VRMC_vrm_animation.expressions.schema.json](schema/VRMC_vrm_animation.expressions.schema.json)

#### expressions.preset.(プリセット表情名)

ひとつのプリセット表情を表すオブジェクトです。
`VRMC_vrm` 拡張で定義されているプリセット表情の名前をキー名として持ちます（`aa` や `leftBlink` など）。
ただし、プリセット表情のうち `lookUp` ・ `lookDown` ・ `lookLeft` ・ `lookRight` は定義できません。

- 型: `expressions.expression`
- 必須: No

### expressions.custom

カスタム表情に対する定義を含むオブジェクトです。

#### Properties

||型|説明|必須|
|:-|:-|:-|:-|
|(カスタム表情名)|`expressions.expression`|ひとつのカスタム表情|No|

#### JSON Schema

[VRMC_vrm_animation.expressions.schema.json](schema/VRMC_vrm_animation.expressions.schema.json)

#### expressions.custom.(プリセット表情名)

ひとつのカスタム表情を表すオブジェクトです。
`VRMC_vrm` 拡張で定義されるカスタム表情の名前と対応します。

- 型: `expressions.expression`
- 必須: No

### expressions.expression

ひとつの表情を表すオブジェクトです。

#### Properties

||型|説明|必須|
|:-|:-|:-|:-|
|`node`|`integer`|表情に対応するノードのインデックス|✅ Yes|

#### JSON Schema

[VRMC_vrm_animation.expressions.expression.schema.json](schema/VRMC_vrm_animation.expressions.expression.schema.json)

#### expressions.expression.node ✅

表情に対応するノードのインデックスです。

- 型: `integer`
- 必須: Yes

---

### lookAt

LookAtの注視点とノードの対応関係を表すオブジェクトです。

#### Properties

||型|説明|必須|
|:-|:-|:-|:-|
|`node`|`integer`|注視点に対応するノードのインデックス|✅ Yes|

#### JSON Schema

[VRMC_vrm_animation.lookAt.schema.json](schema/VRMC_vrm_animation.lookAt.schema.json)

#### lookAt.node ✅

注視点に対応するノードのインデックスです。

- 型: `integer`
- 必須: Yes
