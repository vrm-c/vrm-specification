# VRMC_materials_mtoon 

## Contributors

## Dependencies

Written against the glTF 2.0 spec.

## Overview

この拡張は、 VRM 向けのトゥーンシェーダ実装です。
トゥーンシェーダ的な効果を付与しつつ、 PBR のシーンライティングと協調することを目的とします。
日本の手描きアニメ表現への特化はしません。

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

                }
            }
        }
    ]
}
```

## Definition

### Types
型定義のうち Color と Texture は glTF に準じて Linear Colorspace で格納されます。

### Coordinates
UV 座標系について


### BRDF


### Meta

MToon 自体のメタ情報に関する定義を述べます。

#### MToon Defined Properties

|         | 型      | 説明             | 必須 |
|---------|---------|----------------|:-----|
| version | integer | この拡張のバージョン番号 | ✅    |

#### version

この拡張のバージョン番号を示します。

今後、仕様と異なる重篤なバグが実装に発覚し、それを修正することによって多くのモデルに影響が及ぶ事態が発生した場合に、このバージョン情報を参照して Migration を行うような用途を想定しています。


### Rendering

描画に関する MToon の定義を述べます。

#### Render Mode

このマテリアルがどのようなアルファ処理で描画されるのかを指定します。
アルファ値は、 glTF の仕様で定義される `pbrMetallicRoughness.baseColorFactor` および `pbrMetallicRoughness.baseColorTexture` で定義されるアルファ値を参照します。

glTF で定義される `alphaMode` に加え、本拡張では `transparentWithZWrite` というプロパティを提供します。
このプロパティが `true` の場合、 `alphaMode` が `BLEND` の際に、本来の処理に加えて ZBuffer に対する書き込みを推奨します。
この実装が困難な場合、通常のアルファブレンディングにフォールバックしてください。

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


### Color
色に関する定義を述べます。

#### Dependencies
- [`pbrMetallicRoughness.baseColorFactor`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#metallic-roughness-material)
- [`pbrMetallicRoughness.baseColorTexture.index`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#metallic-roughness-material)
- [`alphaCutoff`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#alpha-coverage)

#### MToon Defined Properties
|                       |   型   |                         説明                          |
| :-------------------- | :----- | :---------------------------------------------------- |
| TransparentWithZWrite | bool   | RenderMode が TransparentWithZWrite であるとき `true` |
| ShadeColor            | float4 | ShadeColor の色成分                                   |
| ShadeMultiplyTexture  | int    | ShadeColor のテクスチャ。Buffer の index を指す       |

#### Lit Color
Albedo を表します。
`pbrMetallicRoughness.baseColorFactor` および `pbrMetallicRoughness.baseColorTexture` に格納されます。

### Shade Color
光に当たらない部分の色合いを表します。
`ShadeColor` および `ShadeMultiplyTexture` に格納されます。

### Cutout Threshold
Cutout 処理におけるアルファ値のしきい値を表します。
これは glTF の `alphaCutoff` に準拠します。

#### Implementation



### Lighting
ライティングに関する定義を述べます。
    
#### Dependencies

#### MToon Defined Properties
|                            |  型   | Range |                 説明                 |
| :------------------------- | :---- | :---- | :----------------------------------- |
| ShadingShiftValue          | float | [0,1] | シェーディング境界をシフトする値     |
| ShadingToonyValue          | float | [0,1] | シェーディング境界を平滑化する値     |
| LightColorAttenuationValue | float | [0,1] | 入射する光の彩度成分に対する減衰係数 |
| GiIntensityValue           | float | [0,1] | 大域照明の寄与係数                   |

#### Lighting Model
MToon は Lambert 反射モデルを拡張します。

- `ShadingShiftValue`
- `ShadingToonyValue`

#### Lighting Contribution

- `LightColorAttenuationValue`
- `GiIntensityValue`

#### Implementation



### Emission
エミッション成分に関する定義を述べます。

#### Dependencies
- [`emissiveFactor`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#additional-maps)
- [`emissiveTexture.index`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#additional-maps)

#### MToon Defined Properties

#### Emissive Factor
入射光に因らず自己発光する成分を表します。
これは glTF の `emissiveFactor` および `emissiveTexture` に準拠します。

#### Implementation



### MatCap
MatCap 成分に関する定義を述べます。

#### Dependencies

#### MToon Defined Properties
|                 |  型  |                         説明                          |
| :-------------- | :--- | :---------------------------------------------------- |
| AdditiveTexture | int  | 加算 MatCap 成分のテクスチャ。 Buffer の index を指す |

定義名変えたほうが良さそう

#### MatCap Factor
MatCap は View Normal Vector を基にマッピングする手法です。
主に事前にライティングを焼き込む用途で広く使われています。
MToon はこれを加算の形で実装しています。
テクスチャは `AdditiveTexture` に格納されます。

#### Implementation



### Rim
Rim Lighting 成分に関する定義を述べます。

#### Dependencies

#### MToon Defined Properties
|                 |  型  | Range |                         説明                          |
| :-------------- | :--- |:---| :---------------------------------------------------- |
| RimColor | float4 | N/A | Rim 乗算成分の色 |
| RimMultiplyTexture | int  | N/A | Rim 乗算成分の寄与係数テクスチャ。 Buffer の index を指す |
| RimLightingMixValue | float | [0, 1] | Rim 乗算成分の入射光寄与成分。0 は自己発光、1 は Albedo 扱い |
| RimFresnelPowerValue | float | [0, 100] | Rim 乗算成分のフレネル係数 |
| RimLiftValue | float | [0, 1] | Rim 乗算成分の加算項 |

#### Rim Factor
Rim は View Normal Vector を基にして、オブジェクトの縁に疑似 Rim Lighting を与える手法です。
色は `RimColor` および `RimMultiplyTexture` に格納されます。
Rim を自己発光とするか Albedo とするかの切替係数は `RimLightingMixValue` に格納されます。
また計算の係数は `RimFresnelPowerVale` および `RimLiftValue` に格納されます。

#### Implementation



### NormalDefinition
法線に関する定義を述べます。

#### Dependencies
- [`normalTexture.index`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#additional-maps)
- [`normalTexture.scale`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#additional-maps)

#### MToon Defined Properties


#### Normal
法線は glTF の `normalTexture` に準拠します。

#### Implementation



### Outline
輪郭線描画に関する定義を述べます。

#### Dependencies

#### MToon Defined Properties
|                              |   型   |  Range  |                                  説明                                  |
| :--------------------------- | :----- | :------ | :--------------------------------------------------------------------- |
| OutlineWidthMode             | int    | [0, 2]  | 輪郭線の描画モード。0 が無し、1 がワールド座標系、2 がスクリーン座標系 |
| OutlineWidthValue            | float  | [0, 1]  | 輪郭線幅                                                               |
| OutlineWidthMultiplyTexture  | int    | N/A     | 輪郭線幅指定テクスチャ                                                 |
| OutlineScaledMaxDitanceValue | float  | [1, 10] | 輪郭線の描画モードがスクリーン座標系のとき輪郭線を太くする限界距離     |
| OutlineColorMode             | int    | [0,1]   | 輪郭線色に、通常描画の色を乗算するかどうかのフラグ                     |
| OutlineColor                 | float4 | N/A     | 輪郭線色                                                               |
| OutlineLightingMixValue      | float  | [0,1]   | 輪郭線を Emission 扱いするか Albedo 扱いするかのフラグ                 |

#### Outline
輪郭線はトゥーンシェーダにおける大切なファクタのひとつです。
輪郭線は通常、マルチパスで描画されます。
MToon の輪郭線は Skinning 後の頂点情報を基に計算されます。
この頂点情報には移動後の位置、回転後の法線が含まれます。



#### Implementation


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

> View Normal Vector をもとに計算される `AdditiveTexture` テクスチャについては、本格長による UV アニメーションの対象外となります。

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
アサインされていない場合、マスクは適用されず、数値で設定した値がそのまま適用されます。

マスクのように使われることを想定しています。

アサインされたテクスチャのBコンポーネントを参照します。

> アサインされたテクスチャのBコンポーネントを参照するため、白黒のマスクテクスチャを使用することもできますし、チャンネルごとに他のマスクを持ったRGBのテクスチャを利用することもできます。 `shadingToonyMultiplyTexture` (Rチャンネルを使う) および `outlineWidthMultiplyTexture` (Gチャンネルを使う) を組み合わせることができます。

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


## Extension compatibility and fallback materials
MToon の実装が難しい場合 `KHR_materials_unlit` にフォールバックしてください。

