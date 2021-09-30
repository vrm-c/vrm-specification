# VRMC_materials_mtoon

*Version 1.0-beta*

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Contributors](#contributors)
- [Dependencies](#dependencies)
- [Overview](#overview)
- [Extending Materials](#extending-materials)
- [Definition](#definition)
  - [Types](#types)
  - [Vertex Colors](#vertex-colors)
  - [Coordinates](#coordinates)
  - [BRDF](#brdf)
  - [Meta](#meta)
    - [MToon Defined Properties](#mtoon-defined-properties)
    - [specVersion](#specversion)
  - [Rendering](#rendering)
    - [Render Mode](#render-mode)
    - [Render Queue](#render-queue)
    - [MToon Defined Properties](#mtoon-defined-properties-1)
    - [transparentWithZWrite](#transparentwithzwrite)
    - [renderQueueOffsetNumber](#renderqueueoffsetnumber)
    - [Double Sided](#double-sided)
  - [Lighting](#lighting)
    - [Lighting Model](#lighting-model)
    - [Lit Color](#lit-color)
    - [Shade Color](#shade-color)
    - [Surface Normal](#surface-normal)
    - [Shading Shift](#shading-shift)
    - [Implementation](#implementation)
    - [MToon Defined Properties](#mtoon-defined-properties-2)
    - [shadeColorFactor](#shadecolorfactor)
    - [shadeMultiplyTexture](#shademultiplytexture)
    - [shadingShiftFactor](#shadingshiftfactor)
    - [shadingShiftTexture](#shadingshifttexture)
    - [shadingToonyFactor](#shadingtoonyfactor)
  - [shadingShiftTextureInfo](#shadingshifttextureinfo)
    - [Properties](#properties)
    - [shadingShiftTextureInfo.index ✅](#shadingshifttextureinfoindex-)
    - [shadingShiftTextureInfo.texCoord](#shadingshifttextureinfotexcoord)
    - [shadingShiftTextureInfo.scale](#shadingshifttextureinfoscale)
  - [Global Illumination](#global-illumination)
    - [MToon Defined Properties](#mtoon-defined-properties-3)
    - [giIntensityFactor](#giintensityfactor)
  - [Emission](#emission)
  - [Rim Lighting](#rim-lighting)
    - [MatCap](#matcap)
    - [Parametric Rim Lighting](#parametric-rim-lighting)
    - [Rim Multiply Texture](#rim-multiply-texture)
    - [Rim Lighting Mix](#rim-lighting-mix)
    - [Implementation](#implementation-1)
    - [MToon Defined Properties](#mtoon-defined-properties-4)
    - [matcapFactor](#matcapfactor)
    - [matcapTexture](#matcaptexture)
    - [parametricRimColorFactor](#parametricrimcolorfactor)
    - [parametricRimFresnelPowerFactor](#parametricrimfresnelpowerfactor)
    - [parametricRimLiftFactor](#parametricrimliftfactor)
    - [rimMultiplyTexture](#rimmultiplytexture)
    - [rimLightingMixFactor](#rimlightingmixfactor)
  - [Outline](#outline)
    - [Outline Width](#outline-width)
    - [Outline Lighting Mix](#outline-lighting-mix)
    - [Implementation](#implementation-2)
    - [MToon Defined Properties](#mtoon-defined-properties-5)
    - [outlineWidthMode](#outlinewidthmode)
    - [outlineWidthFactor](#outlinewidthfactor)
    - [outlineWidthMultiplyTexture](#outlinewidthmultiplytexture)
    - [outlineColorFactor](#outlinecolorfactor)
    - [outlineLightingMixFactor](#outlinelightingmixfactor)
  - [UV Animation](#uv-animation)
    - [Compatibility with KHR_texture_transform](#compatibility-with-khr_texture_transform)
    - [MToon Defined Properties](#mtoon-defined-properties-6)
    - [uvAnimationMaskTexture](#uvanimationmasktexture)
    - [uvAnimationScrollXSpeedFactor](#uvanimationscrollxspeedfactor)
    - [uvAnimationScrollYSpeedFactor](#uvanimationscrollyspeedfactor)
    - [uvAnimationRotationSpeedFactor](#uvanimationrotationspeedfactor)
- [Extension compatibility and fallback materials](#extension-compatibility-and-fallback-materials)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

## Dependencies

Written against the glTF 2.0 spec.

### `KHR_materials_unlit` との相互作用

`VRMC_materials_mtoon` と併せてマテリアルに `KHR_materials_unlit` が設定されている場合、 `VRMC_materials_mtoon` を優先して解釈してください。

MToonシェーダの実装が難しい場合、 `KHR_materials_unlit` にフォールバックしてください。

## Overview

この拡張は、 VRM 向けのトゥーンシェーダ実装です。

トゥーンシェーダ的な効果を付与しつつ、 PBR のシーンライティングと協調することを目的とします。
日本の手描きアニメ表現への特化はしません。

その際、本来トゥーンシェーダは画像処理的なアプローチを必要としますが、本拡張ではそれを避けて近似定義を行います。
したがって本定義では、特定のレンダリング手法に依存した定義を含みます。

## Extending Materials

マテリアルの `extensions` に定義します。

```json
{
    "materials": [
        {
            "name": "MyUnlitMaterial",
            "pbrMetallicRoughness": {
                "baseColorFactor": [ 0.5, 0.8, 0.0, 1.0 ]
                // texture
            },
            // emission

            "extensions": {
                "VRMC_materials_mtoon": {
                    "specVersion": "1.0-beta",
                    // ...
                }
            }
        }
    ]
}
```

## Definition

### Types
型定義のうち Color と Texture は glTF に準じて Linear Colorspace で格納されます。

### Vertex Colors

MToonのマテリアルについては、頂点カラーを無視します。

### Coordinates
UV 座標系について


### BRDF


### Meta

MToon 自体のメタ情報に関する定義を述べます。

#### MToon Defined Properties

|             | 型       | 説明             | 必須  |
|-------------|----------|----------------|:------|
| specVersion | `string` | この拡張のバージョン番号 | ✅ Yes |

#### specVersion

VRMC_materials_mtoon 拡張のバージョン番号を示します。
値は `"1.0-beta"` です。

- 型: `string`
- 必須: Yes

### Rendering

描画に関する MToon の定義を述べます。

#### Render Mode

このマテリアルがどのようなアルファ処理で描画されるのかを指定します。
アルファ値は、 glTF の仕様で定義される `pbrMetallicRoughness.baseColorFactor` および `pbrMetallicRoughness.baseColorTexture` で定義されるアルファ値を参照します。
アルファ値の処理は、 glTF の仕様で定義される `alphaMode` および `alphaCutoff` を用います。
それぞれの処理の詳細については、 glTF の仕様に準拠します。

glTF で定義される `alphaMode` に加え、本拡張では `transparentWithZWrite` というプロパティを提供します。
通常 `alphaMode` が `BLEND` の際は、 ZBuffer に対する書き込みを推奨しませんが、
このプロパティが `true` の場合、 `alphaMode` が `BLEND` の際に、 ZBuffer に対する書き込みを推奨します。

> `transparentWithZWrite` の実装が困難な場合、通常のアルファブレンディングの処理にフォールバックしてください。

#### Render Queue

このマテリアルがどのような順番で描画されるべきなのかを指定します。
MToon は次のような順番で描画されることを期待します。

1. `alphaMode` が `OPAQUE`
2. `alphaMode` が `MASK`
3. `alphaMode` が `BLEND` かつ `transparentWithZWrite` が `true`
3. `alphaMode` が `BLEND` かつ `transparentWithZWrite` が `false`

トゥーンシェーダでは半透明表現が多用されるため、描画順序に起因する問題が発生しがちです。
MToon ではそのために `transparentWithZWrite` を導入していますが、さらに描画順制御の仕組みを導入しています。
`renderQueueOffsetNumber` はそれぞれの Render Mode のデフォルト描画順に対するオフセット値です。
Unity におけるマテリアルごとの Render Queue 値に対してオフセットとして加算される挙動を期待します。
`renderQueueOffsetNumber` は `alphaMode` が `BLEND` のときに作用します。
値が大きいほど、描画の順番は後回しにされます。
値のとりうる範囲について次の表に示します。

|                                                            | Min Value | Max Value |
|:-----------------------------------------------------------|:----------|:----------|
| `alphaMode` が `OPAQUE`                                     | `0`       | `0`       |
| `alphaMode` が `MASK`                                       | `0`       | `0`       |
| `alphaMode` が `BLEND` かつ `transparentWithZWrite` が `true`  | `0`       | `+9`      |
| `alphaMode` が `BLEND` かつ `transparentWithZWrite` が `false` | `-9`      | `0`       |

`renderQueueOffsetNumber` がどのような値になったとしても、前述の Render Queue による描画の順番の方が優先されます。

MToon ではない他の glTF モデルなどと併せてレンダリングを行う場合、それらの `renderQueueOffsetNumber` は `0` と仮定して描画順を制御してください。

Unity における Render Queue 相当の描画順制御が実装で困難な場合は、 `renderQueueOffsetNumber` が `0` であるとみなしてください。

#### MToon Defined Properties

|                           | 型        | 説明                                             | 必須                |
|:--------------------------|:----------|:-----------------------------------------------|:------------------|
| `transparentWithZWrite`   | `boolean` | `alphaMode` が `BLEND` のとき、 ZBuffer への書き込みを行うか | No, 初期値: `false` |
| `renderQueueOffsetNumber` | `integer` | 描画順に対するオフセット値                               | No, 初期値: `0`     |

#### transparentWithZWrite

`alphaMode` が `BLEND` のとき、 ZBuffer への書き込みを行うかを指定します。

- 型: `boolean`
- 必須: No, 初期値: `false`

#### renderQueueOffsetNumber

前述した Render Queue のオフセット値を指定します。

- 型: `integer`
- 必須: No, 初期値: `0`

#### Double Sided

両面ポリゴンは glTF コア仕様のマテリアル定義に含まれる、 `doubleSided` を用いる Double Sided の定義に準じます。

ただし本拡張が定義する輪郭線 ([Outline](#Outline) を参照) の描画は `doubleSided` の状態に関わらず、常に front-face culling が有効です。

### Lighting

ライティングに関する定義を述べます。

#### Lighting Model

MToon は Lambert 反射モデルを拡張します。
通常の Lambert 反射モデルと同様、面の法線とライトベクトルによって陰影が付けられますが、以下のような特徴を持つことで、トゥーン表現を可能とします:

- 通常のベースカラーとは別に、陰色の指定ができます。
- 陰の境界の位置および広さ（ぼかし具合）を調整することができます。

#### Lit Color

ベースカラーは、 glTF コア仕様のマテリアル定義に含まれる `pbrMetallicRoughness.baseColorFactor` および `pbrMetallicRoughness.baseColorTexture` を用います。

#### Shade Color

MToonでは、ベースカラーとは別に、陰色の指定ができます。

通常の Lambert 反射モデルの場合、光の当たっていない陰の面は黒くレンダリングされますが、 MToon ではベースカラーと陰色が線形補間されるような挙動となります。
また、ベースカラーがテクスチャによって乗算されるのと同様、陰色に対しても陰色用のテクスチャを別途指定することができ、テクスチャ色が陰色に対して乗算されます。

陰色は、 MToon 拡張によって定義される `shadeColorFactor` および `shadeMultiplyTexture` を用います。

<img src="figures/mtoon-lit-shade.png" width="67%">

#### Surface Normal

法線マップを利用できます。 glTF コア仕様のマテリアル定義に含まれる `normalTexture` を用います。

#### Shading Shift

MToon では、面の法線とライトベクトルの内積に応じてベースカラーと陰色を線形補間しますが、このベースカラーと陰色の境界の位置および広さ（ぼかし具合）を調整することができます。

シェーディング境界の位置は、 MToon 拡張によって定義される `shadingShiftFactor` および `shadingShiftTexture` を用います。
シェーディング境界の広さ（ぼかし具合）は、 MToon 拡張によって定義される `shadingToonyFactor` を用います。

`shadingShiftTexture` は、 `shadingShiftFactor` によって設定されるシェーディング境界の位置を調整します。
このテクスチャによって、部分的に光のあたり方を調整することができます。
`shadingShiftTexture` は、 `shadingShiftFactor` と加算で処理されます。
また、 `shadingShiftTexture.scale` を用いて、このテクスチャがシェーディング境界にどの程度寄与するかを制御することができます。

<img src="figures/mtoon-shading-ramp.png" width="67%">

#### Implementation

以下に、擬似コードでライティング処理の実装例を示します:

```
function linearstep( a: Number, b: Number, t: Number ): Number
  return saturate( ( t - a ) / ( b - a ) )
end function

let shading: Number = dot( N, L )
shading = shading + shadingShiftFactor
shading = shading + texture( shadingShiftTexture, uv ) * shadingShiftTexture.scale
shading = linearstep( -1.0 + shadingToonyFactor, 1.0 - shadingToonyFactor, shading )

let baseColorTerm: ColorRGB = baseColorFactor.rgb * texture( baseColorTexture, uv ).rgb
let shadeColorTerm: ColorRGB = shadeColorFactor.rgb * texture( shadeMultiplyTexture, uv ).rgb

let color: ColorRGB = lerp( baseColorTerm, shadeColorTerm, shading )
color = color * lightColor
```

#### MToon Defined Properties

|                      | 型          | 説明                           | 必須                     |
|:---------------------|:------------|:------------------------------|:-------------------------|
| shadeColorFactor     | `number[3]` | Shade色                        | No, Default: `[0, 0, 0]` |
| shadeMultiplyTexture | `object`    | Shade色の乗算テクスチャ              | No                       |
| shadingShiftFactor   | `number`    | シェーディング境界をシフトする値            | No, Default: `0.0`       |
| shadingShiftTexture  | `object`    | シェーディング境界をシフトするテクスチャ         | No                       |
| shadingToonyFactor   | `number`    | シェーディング境界を平滑度合いを指定する値 | No, Default: `0.9`       |

#### shadeColorFactor

陰色を指定します。
値はリニア色空間で評価されます。

- 型: `number[3]`
- 必須: No, 初期値: `0.0`

#### shadeMultiplyTexture

陰色に乗算されるテクスチャを指定します。

テクスチャの値は sRGB 伝達関数でエンコードされています。
アサインされたテクスチャの RGB コンポーネントを参照し、リニア色空間に変換して評価されます。

`shadeColorFactor` で設定された値に対して乗算されます。
アサインされていない場合、 `shadeColorFactor` で設定した値がそのまま適用されます。

- 型: `object`
- 必須: No

#### shadingShiftFactor

シェーディング境界をシフトする値を指定します。

具体的にどう計算がされるかについては、上記 [Shading Shift](#Shading%20Shift) を参照ください。

- 型: `number`
- 必須: No, 初期値: `0.0`

#### shadingShiftTexture

シェーディング境界をシフトするテクスチャを指定します。

具体的にどう計算がされるかについては、上記 [Shading Shift](#Shading%20Shift) を参照ください。

テクスチャの値はリニア色空間です。
アサインされたテクスチャの R コンポーネントを参照し、リニア色空間で評価されます。

> アサインされたテクスチャのRコンポーネントを参照するため、白黒のマスクテクスチャを使用することもできますし、チャンネルごとに他のマスクを持ったRGBのテクスチャを利用することもできます。
> `outlineWidthMultiplyTexture` (Gチャンネルを使う) および `uvAnimationMaskTexture` (Bチャンネルを使う) を組み合わせることができます。

- 型: `object`
- 必須: No

#### shadingToonyFactor

シェーディング境界の広さ（ぼかし）を指定します。

具体的にどう計算がされるかについては、上記 [Shading Shift](#Shading%20Shift) を参照ください。

- 型: `number`
- 必須: No, 初期値: `0.9`

### shadingShiftTextureInfo

`shadingShiftTexture` でテクスチャを指定するオブジェクトです。

#### Properties

|          | 型        | 説明                             | 必須              |
|:---------|:----------|:---------------------------------|:------------------|
| index    | `integer` | テクスチャのindex                      | ✅ Yes             |
| texCoord | `integer` | テクスチャマッピングに用いるTEXCOORD          | No, 初期値: `0`   |
| scale    | `number`  | テクスチャのシェーディング境界への寄与を指定する値 | No, 初期値: `1.0` |

#### shadingShiftTextureInfo.index ✅

テクスチャのindexを指定します。

- 型: `integer`
- 必須: Yes
- 最小値: `>= 0`

#### shadingShiftTextureInfo.texCoord

テクスチャマッピングをするに際し、参照するTEXCOORDを指定します。

- 型: `integer`
- 必須: No, 初期値: `0`
- 最小値: `>= 0`

#### shadingShiftTextureInfo.scale

テクスチャのシェーディング境界への寄与を指定します。
この値はリニア値です。

- 型: `number`
- 必須: No, 初期値: `1.0`

### Global Illumination

IBL (Image-based Lighting) や SH (Spherical Harmonics) Lighting などの特定の位置や方向に依存しない、いわゆる大域照明に対する挙動の定義を述べます。

一般にトゥーンシェーダにおいて、描画された陰影を見てジオメトリの凹凸を詳細に読み取れる表現は望ましくありません。
そのため本拡張では Shading Toony と Shading Shift のパラメタを導入しています。
これらのパラメタはジオメトリの凹凸、つまり法線方向と光源の入射方向の変化に対して陰影が大きく変化しないように制御することができます。

しかし IBL や SH Lighting といった大域照明においては、これらのパラメタでは不十分です。
大域照明は方向が変化すると、放射輝度も大きく変化するからです。

そこで本拡張では GI Equalization Factor のパラメタを導入します。
GI Equalization Factor はジオメトリの法線に依らず、ジオメトリが受ける大域照明を一定とすることができます。
その結果として描画される陰影は弱くなり、ジオメトリの凹凸を読み取りづらい表現にすることができます。
具体的には、大域照明を方向に対して平滑化することで実現します。

GI Equalization Factor は MToon 拡張によって定義される `giEqualizationFactor` を用います。

詳細な計算定義を以下に述べます。

まず、任意のジオメトリの法線ベクトルを `n` とします。
また任意の方向ベクトル `x` に対応するレンダリングシステム上の大域照明を求める関数を `rawGi(x)` とします。
そして、均一化された大域照明 `uniformedGi` を定義します。
`uniformedGi` は理想的には全方向積分を用いて平均値を計算することが求められます。
しかしリアルタイムレンダリングにおいては負荷が高いため、各実装において近似実装を行ってください。
たとえば Spherical Harmonics の低次の項のみ扱う実装などです。
ただし Spherical Harmonics の低次の項のみの場合、強い平行光源下 (例: 太陽光) においてうまく近似できない場合があります。
そこでここでは 2 点サンプリングの平均を近似実装例として挙げます。

`uniformedGi = (rawGi([0, 1, 0]) + rawGi([0, -1, 0])) / 2`

このとき、任意のジオメトリの法線ベクトル `n` に対応する大域照明 `gi(n)` は次のように計算します。

`gi(n) = lerp(rawGi(n), uniformedGi, giEqualization)`

そして、 Lit Color を Diffuse とみなして次のライティング計算をします。

`giLighting = gi(n) * litColor`

#### Implementation

以下に、擬似コードで大域照明処理の実装例を示します:

```
let giEqualizationFactor: number

let worldUpVector: Vector3 = Vector3(0, +1, 0)
let worldDownVector: Vector3 = Vector3(0, -1, 0)

let uniformedGi: ColorRGB = (rawGi(worldUpVector) + rawGi(worldDownVector)) / 2.0
let passthroughGi: ColorRGB = rawGi(normal)

let gi: ColorRGB = lerp(passthroughGi, uniformedGi, giEqualizationFactor)

-- color にはライティング結果が含まれているものとする
color = color + gi * litColor
```

#### MToon Defined Properties

|                   | 型       | 説明              | 必須               |
|:------------------|:---------|:----------------|:-------------------|
| giEqualizationFactor | `number` | 大域照明の均一化係数 | No, Default: `0.9` |

#### giEqualizationFactor

Global Illumination における、均一化度合いを定義します。
`0` のとき大域照明はそのままの値で評価されます。
`1` に近づくほど大域照明は方向に対する平滑化が強まって均一化された値で評価されます。

具体的にどう計算がされるかについては、上記 [Global Illumination](#Global%20Illumination) を参照ください。

- 型: `number`
- 必須: No, 初期値: `0.9`

### Emission

エミッションを利用できます。
glTF コア仕様のマテリアル定義に含まれる `emissiveFactor` および `emissiveTexture` を用います。

### Rim Lighting

リムライティングに関する定義を述べます。

#### MatCap

MatCap は View Normal Vector を基にマッピングする手法です。
主に事前にライティングを焼き込む用途で広く使われています。
MatCapは、ライティング結果に対して加算されます。

テクスチャは、MToon拡張によって定義される `matcapTexture` に格納されます。
また、 `matcapFactor` によって値が乗算されます。

#### Parametric Rim Lighting

パラメトリックリムライトは、 View Normal Vector を基にして、オブジェクトの縁に疑似リムライト効果を与える手法です。
パラメトリックリムライトは、ライティング結果に対して加算されます。

MToon拡張によって定義される `parametricRimColorFactor` という値で、パラメトリックリムライトの色を制御できます。

また、MToon拡張によって定義される `parametricRimFresnelPowerFactor` ・ `parametricRimLiftFactor` という値で、パラメトリックリムライトの形状を制御することができます。
形状は、 `pow( saturate( 1.0 - dot( N, V ) + parametricRimLiftFactor ), parametricRimFresnelPowerFactor )` という数式で求められます。

#### Rim Multiply Texture

特定の箇所だけリムライティングを出す・消すといった制御をテクスチャを用いて行うことができます。
UV マッピングされたテクスチャの値がリムライト色に対して乗算されます。

テクスチャは、MToon拡張によって定義される `rimMultiplyTexture` に格納されます。

#### Rim Lighting Mix

リムライティングが周囲の光源からどのくらい影響を受けるかを制御することができます。
まったく影響を受けない場合、エミッションのように自己発光するリムライティングとなります。
影響を受ける場合、光源の影響がリムライティングの色に乗算されます。

MToon拡張によって定義される `rimLightingMixFactor` で設定した値に応じて、光源の影響度合いが線形に変化します。

#### Implementation

以下に、擬似コードでリムライティング処理の実装例を示します:

```
let rim: ColorRGB

let worldViewX: Vector3 = normalize( Vector3( V.z, 0.0, -V.x ) )
let worldViewY: Vector3 = cross( V, x )

let matcapUv: Vector2 = Vector2( dot( x, N ), dot( y, N ) ) * 0.495 + 0.5

rim = matcapFactor * texture( matcapTexture, matcapUv ).rgb

let parametricRim: Number = saturate( 1.0 - dot( N, V ) + parametricRimLiftFactor )
parametricRim = pow( parametricRim, parametricRimFresnelPowerFactor )

rim = rim + parametricRim * parametricRimColorFactor

rim = rim * texture( rimMultiplyTexture, uv ).rgb

rim = rim * lerp( ColorRGB( 1.0, 1.0, 1.0 ), lighting, rimLightingMixFactor )

-- color にはライティング結果が含まれているものとする
color = color + rim
```

#### MToon Defined Properties

|                                 | 型          | 説明                      | 必須                    |
|:--------------------------------|:------------|:--------------------------|:------------------------|
| matcapFactor                    | `number[3]` | MatCap テクスチャに乗算される色    | No, 初期値: `[1, 1, 1]` |
| matcapTexture                   | `object`    | MatCap テクスチャ              | No                      |
| parametricRimColorFactor        | `number[3]` | パラメトリックリムライトの色           | No, 初期値: `[0, 0, 0]` |
| parametricRimFresnelPowerFactor | `number`    | パラメトリックリムライトのフレネル係数     | No, 初期値: `5.0`       |
| parametricRimLiftFactor         | `number`    | パラメトリックリムライトの加算項       | No, 初期値: `0.0`       |
| rimMultiplyTexture              | `object`    | リムライティングに対して乗算されるテクスチャ | No                      |
| rimLightingMixFactor            | `number`    | リムライティングの光源からの影響の割合 | No, 初期値: `1.0`       |

#### matcapFactor

MatCap テクスチャに乗算する色を指定します。
値はリニア色空間で評価されます。

- 型: `number[3]`
- 必須: No, 初期値: `[1, 1, 1]`

#### matcapTexture

MatCap テクスチャを指定します。

テクスチャの値は sRGB 伝達関数でエンコードされています。
アサインされたテクスチャの RGB コンポーネントを参照し、リニア色空間に変換して評価されます。

- 型: `object`
- 必須: No

#### parametricRimColorFactor

パラメトリックリムライトの色を指定します。
値はリニア色空間で評価されます。

- 型: `number[3]`
- 必須: No, 初期値: `[0, 0, 0]`

#### parametricRimFresnelPowerFactor

パラメトリックリムライトのフレネル項を指定します。

具体的にどう計算がされるかについては、上記 [Parametric Rim Lighting](#Parametric%20Rim%20Lighting) を参照ください。

- 型: `object`
- 必須: No, 初期値: `5.0`

#### parametricRimLiftFactor

パラメトリックリムライトの加算項を指定します。

具体的にどう計算がされるかについては、上記 [Parametric Rim Lighting](#Parametric%20Rim%20Lighting) を参照ください。

- 型: `object`
- 必須: No, 初期値: `0.0`

#### rimMultiplyTexture

リムライティングに対して乗算されるテクスチャを指定します。

テクスチャの値は sRGB 伝達関数でエンコードされています。
アサインされたテクスチャの RGB コンポーネントを参照し、リニア色空間に変換して評価されます。

- 型: `object`
- 必須: No

#### rimLightingMixFactor

リムライティングが周囲の光源からどのくらい影響を受けるかを設定します。

- 型: `number`
- 必須: No, 初期値: `1.0`

### Outline

輪郭線描画に関する定義を述べます。

輪郭線はトゥーンシェーダにおける大切なファクタのひとつです。

#### Outline Width

アウトラインの幅は、 MToon 拡張によって定義される `outlineWidthMode` ・ `outlineWidthFactor` ・ `outlineWidthTexture` の値に応じて計算されます。

`outlineWidthMode` が `none` の場合、輪郭線は表示されません。
`outlineWidthMode` が `worldCoordinates` の場合、輪郭線の幅はワールド座標系の距離に応じて決定されます。
`outlineWidthMode` が `screenCoordinates` の場合、輪郭線の幅はスクリーン座標系に依存して決定され、距離に関わらず常に一定の太さとなります。

単位は、 `outlineWidthMode` が `worldCoordinates` の場合はメートル、 `screenCoordinates` の場合は画面の縦幅に対する割合とします。

また、 テクスチャ `outlineWidthTexture` を用いてアウトライン幅を部分ごとに調整することができます。
UV マッピングされたテクスチャの値がアウトライン幅に対して乗算されます。
特定の箇所だけアウトラインを出したくない場合に、テクスチャを用いてマスクをかけるような使い方を想定しています。

> 基本的には、モデラーが `outlineWidthMode` および `outlineWidthFactor` で設定された幅で輪郭線を描画することが推奨されますが、
> アプリケーションによっては、モデルとカメラが十分に近い場合のみ指定された幅で描画し、モデルとカメラが遠い場合は輪郭線を細くするような挙動を実現したいケースもあり得ます（VRM0系における `OutlineScaledMaxDistance` の挙動）。
> 実際の輪郭線の幅は、アプリケーションの需要に応じて設定してください。

#### Outline Lighting Mix

輪郭線の色に対して、表面のシェーディング結果の影響を及ぼすことができます。
シェーディング結果のうち、前述した [Lighting](Lighting) の計算結果が、アウトライン色に対して乗算されます。
MToon 拡張によって定義される `outlineLightingMixFactor` の値に応じて、シェーディング結果の影響をまったく受けない状態と完全に受ける状態が線形に変化します。

#### Implementation

ラスタライズベースのレンダリングパイプラインにおいては、輪郭線は通常、面とは別のレンダリングパスで描画されます。
MToon の輪郭線は Skinning 後の頂点情報を基に計算されます。
この頂点情報には移動後の位置、回転後の法線が含まれます。

> mesh をマルチパスで描画するのが難しい環境・マルチパス描画の負荷が高い環境では、輪郭線の描画を省略するフォールバックを行ってください。

#### MToon Defined Properties

|                             | 型          | 説明                                 | 必須                    |
|-----------------------------|-------------|------------------------------------|-------------------------|
| outlineWidthMode            | `string`    | 輪郭線の描画モード                       | No, 初期値: `"none"`    |
| outlineWidthFactor          | `number`    | 輪郭線幅                             | No, 初期値: `0.0`       |
| outlineWidthMultiplyTexture | `object`    | 輪郭線幅指定テクスチャ                    | No                      |
| outlineColorFactor          | `number[3]` | 輪郭線色                             | No, 初期値: `[0, 0, 0]` |
| outlineLightingMixFactor    | `float`     | 輪郭線色に表面のシェーディング結果を乗算する割合 | No, 初期値: `1.0`       |

#### outlineWidthMode

輪郭線の幅をどのように決定するかを指定します。

具体的にどう計算がされるかについては、上記 [Outline Width](#Outline%20Width) を参照ください。

- 型: `string`
- 必須: No, 初期値: `"none"`
- 許可された値:
  - `none`
  - `worldCoordinates`
  - `screenCoordinates`

#### outlineWidthFactor

輪郭線の幅を指定します。

具体的にどう計算がされるかについては、上記 [Outline Width](#Outline%20Width) を参照ください。

- 型: `number`
- 必須: No, 初期値: `0.0`

#### outlineWidthMultiplyTexture

輪郭線の幅を調整するテクスチャです。
アサインされていない場合、 `outlineWidthFactor` で設定した値がそのまま適用されます。

テクスチャの値はリニア色空間です。
アサインされたテクスチャの G コンポーネントを参照し、リニア色空間で評価されます。

> アサインされたテクスチャのGコンポーネントを参照するため、白黒のマスクテクスチャを使用することもできますし、チャンネルごとに他のマスクを持ったRGBのテクスチャを利用することもできます。
> `shadingShiftTexture` (Rチャンネルを使う) および `uvAnimationMaskTexture` (Bチャンネルを使う) を組み合わせることができます。

- 型: `object`
- 必須: No

#### outlineColorFactor

輪郭線の色を指定します。

- 型: `number[3]`
- 必須: No, 初期値: `[0, 0, 0]`

#### outlineLightingMixFactor

輪郭線の色に表面のシェーディング結果を乗算する割合を指定します。
この値を `0.0` にすると、輪郭職はライティングに依らず常に `outlineColorFactor` で指定した色でレンダリングされます。
輪郭線の色に対してライティングの影響を及ぼしたい場合、この値を `1.0` にします。

- 型: `number`
- 必須: No, 初期値: `1.0`

### UV Animation

UV アニメーション機能を用いて、 MToon で用いられるテクスチャをアニメーションさせることができます。
アニメーションは制御ができるものではなく、自動的・恒常的なものです。

UV アニメーションによる座標変換の際に、 UV の各要素が取りうる値の範囲をclampやrepeatなどによって [0.0 - 1.0] にすることは行いません。テクスチャの `wrapS` および `wrapT` が `REPEAT` および `MIRRORED_REPEAT` に設定してあることを想定した仕様です。

UV アニメーションによって作用されるテクスチャは、 MToon で定義しているテクスチャのうち、以下となります:

- `shadeMultiplyTexture`
- `shadingToonyMultiplyTexture`
- `rimMultiplyTexture`
- `outlineWidthMultiplyTexture`

また、 glTF のコア仕様で定義される以下のテクスチャに対しても作用します:

- `pbrMetallicRoughness.baseColorTexture`
- `normalTexture`

> View Normal Vector をもとに計算される `matcapTexture` テクスチャについては、本拡張による UV アニメーションの対象外となります。

> Implementation Note: 本拡張およびコア仕様外で定義されるテクスチャについては、特に規定をしません。必要に応じて、各実装ごとの対応を行ってください。

#### Compatibility with KHR_texture_transform

`KHR_texture_transform` 拡張を利用している場合、 UV アニメーションによるテクスチャの座標変換は `KHR_texture_transform` による座標変換より先に実行されます。

> `KHR_texture_transform` を併用する場合においても、 UV アニメーションによる座標変換の際に、 UV の各要素が取りうる値の範囲をclampやrepeatなどによって [0.0 - 1.0] にすることは行いません。

#### MToon Defined Properties

|                                | 型       | 説明                           | 必須              |
|--------------------------------|----------|------------------------------|-------------------|
| uvAnimationMaskTexture         | `object` | UV アニメーションを行う範囲を指定するテクスチャ | No                |
| uvAnimationScrollXSpeedFactor  | `number` | UV アニメーションの X 方向の移動速度    | No, 初期値: `0.0` |
| uvAnimationScrollYSpeedFactor  | `number` | UV アニメーションの Y 方向の移動速度    | No, 初期値: `0.0` |
| uvAnimationRotationSpeedFactor | `number` | UV アニメーションの回転速度            | No, 初期値: `0.0` |

#### uvAnimationMaskTexture

UV アニメーションを行う範囲を指定するテクスチャです。

`uvAnimationScrollXSpeedFactor` ・ `uvAnimationScrollYSpeedFactor` ・ `uvAnimationRotationSpeedFactor` で設定された数値に対して乗算されます。
アサインされていない場合、各数値で設定した値がそのまま適用されます。

マスクのように使われることを想定しています。

テクスチャの値はリニア色空間です。
アサインされたテクスチャの B コンポーネントを参照し、リニア色空間で評価されます。

> アサインされたテクスチャのBコンポーネントを参照するため、白黒のマスクテクスチャを使用することもできますし、チャンネルごとに他のマスクを持ったRGBのテクスチャを利用することもできます。
> `shadingShiftTexture` (Rチャンネルを使う) および `outlineWidthMultiplyTexture` (Gチャンネルを使う) を組み合わせることができます。

- 型: `object`
- 必須: No

#### uvAnimationScrollXSpeedFactor

UV アニメーションの X 方向の移動速度を指定します。
単位は UV 座標系毎秒となり、 `1.0` の場合は1秒ごとに UV が1スクロールします。
スクロールの向きは、この値が正の場合に UV が正の方向に単調増加する方向です。

- 型: `number`
- 必須: No, 初期値: `0.0`

#### uvAnimationScrollYSpeedFactor

UV アニメーションの Y 方向の移動速度を指定します。
単位は UV 座標系毎秒となり、 `1.0` の場合は1秒ごとに UV が1スクロールします。
スクロールの向きは、この値が正の場合に UV が正の方向に単調増加する方向です。

- 型: `number`
- 必須: No, 初期値: `0.0`

#### uvAnimationRotationSpeedFactor

UV アニメーションの回転速度を指定します。
単位はラジアン毎秒となり、 `1.0` の場合は2π秒ごとに UV が1回転します。
UV 座標系における (0.5, 0.5) を中心に回転します。
回転方向は、 UV 座標系における反時計回りです（i.e. 画像が時計回りに動く向き）。

- 型: `number`
- 必須: No, 初期値: `0.0`
