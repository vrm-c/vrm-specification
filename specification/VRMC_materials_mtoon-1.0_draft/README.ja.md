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

#### Dependencies

#### MToon Defined Properties
|               |  型  |           説明           |
| :------------ | :--- | :----------------------- |
| VersionNumber | int  | この拡張のバージョン番号 |



### Rendering
描画に関する MToon の定義を述べます。

#### Dependencies
- [`alphaMode`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#alpha-coverage)
- [`doubleSided`](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#double-sided)

#### MToon Defined Properties
|                         |  型  |                         説明                          |
| :---------------------- | :--- | :---------------------------------------------------- |
| TransparentWithZWrite   | bool | RenderMode が TransparentWithZWrite であるとき `true` |
| RenderQueueOffsetNumber | int  | RenderMode のデフォルト描画順に対するオフセット値     |

#### RenderMode
このマテリアルがどのようなアルファ処理で描画されるのかを指定します。
モードとして次の 4 つが存在します。

- Opaque - 完全不透明で描画します。アルファ値は無視します。
- Cutout - アルファ値とアルファカットオフ値を基に、完全不透明・完全透明のどちらかで描画します。
- Transparent - アルファ値を基に、背景色とブレンドして描画します。
- TransparentWithZWrite - 描画の仕方は `Transparent` と同じですが、加えて ZBuffer に対して書き込みを行います。

これらのモードは、glTF の `alphaMode` および MToon の `TransparentWithZWrite` の 2 つの組み合わせで定義されます。
これについて次の表に示します。

|                       | `alphaMode` | `TransparentWithZWrite` |
| :-------------------- | :---------- | :---------------------- |
| Opaque                | `OPAQUE`    | `false`                 |
| Cutout                | `MASK`      | `false`                 |
| Transparent           | `BLEND`     | `false`                 |
| TransparentWithZWrite | `BLEND`     | `true`                  |

また `TransparentWithZWrite` の実装が困難なときは `Transparent` にフォールバックしてください。

#### CullMode
このマテリアルがどのようなカリング処理で描画されるのかを指定します。
これは glTF の `doubleSided` に準拠します。

#### RenderQueue
このマテリアルがどのような順番で描画されるべきなのかを指定します。
MToon は次のような順番で描画されることを期待します。

1. Opaque
2. Cutout
3. TransparentWithZWrite
4. Transparent

トゥーンシェーダでは残念ながら半透明表現が多用されるため、描画順序に起因する問題が発生しがちです。
MToon ではそのために `TransparentWithZWrite` を導入していますが、さらに描画順制御の仕組みを導入しています。
`RenderQueueOffsetNumber` はそれぞれの RenderMode のデフォルト描画順に対するオフセット値です。
`RenderQueueOffsetNumber` は RenderMode が `Transparent` `TransparentWithZWrite` のときに作用します。
値が大きいほど、描画の順番は後回しにされます。
値のとりうる範囲について次の表に示します。

|                       | Min Value | Max Value |
| :-------------------- | :-------- | :-------- |
| Opaque                | 0         | 0         |
| Cutout                | 0         | 0         |
| Transparent           | -9        | 0         |
| TransparentWithZWrite | 0         | +9        |

また、どのような値になったとしても、前述の RenderQueue による描画の順番の方が優先されます。
この動作について次の表で例示します。

| Order |      RenderMode       | RenderQueueOffsetNumber |
| :---- | :-------------------- | :---------------------- |
| 1     | Opaque                | N/A                     |
| 2     | Cutout                | N/A                     |
| 3     | TransparentWithZWrite | 0                       |
| 4     | TransparentWithZWrite | +9                      |
| 5     | Transparent           | -9                      |
| 6     | Transparent           | 0                       |

RenderQueueOffset の実装が困難なときは `RenderQueueOffsetNumber` が `0` であるとみなしてください。



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


### UV Coords
UV 座標に関する定義を述べます。

#### Dependencies
- [material.pbrMetallicRoughness.baseColorTexture.extensions.KHR_texture_transform](https://github.com/KhronosGroup/glTF/tree/master/extensions/2.0/Khronos/KHR_texture_transform#khr_texture_transform)

#### MToon Defined Properties
|                               |  型   |                      説明                       |
| :---------------------------- | :---- | :---------------------------------------------- |
| UvAnimationMaskTexture        | int   | UV アニメーションを行う範囲を指定するテクスチャ |
| UvAnimationScrollXSpeedValue  | float | UV アニメーションの X 方向の移動速度            |
| UvAnimationScrollYSpeedValue  | float | UV アニメーションの Y 方向の移動速度            |
| UvAnimationROtationSpeedValue | float | UV アニメーションの回転速度                     |

### Texture Transform
MToon の UV Coords は 2 種類存在します。
1 つは `TEXCOORDS_0` および `baseColorTexture.extensions.KHR_texture_transform` を参照するもの。
もう 1 つは View Normal Vector を基に計算されるものです。
ここで前者は UV アニメーションの影響を受けます。
UV アニメーションは単純な移動と回転で表現されます。


## Extension compatibility and fallback materials
MToon の実装が難しい場合 `KHR_materials_unlit` にフォールバックしてください。

