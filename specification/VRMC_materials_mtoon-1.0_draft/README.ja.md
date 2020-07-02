# VRMC_materials_mtoon 

FIXME: ぜんぶ

PlaceHolder

## Dependencies

Written against the glTF 2.0 spec.

## Overview

この拡張は、 VRM 向けの トゥーンシェーダー(セルシェーダー？) 実装です。
カット毎に設定を特化するというより、シーンのライティングと協調しつつ、トゥーンシェーダー的な効果を付加する方向性です。

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

### Meta
* public int VersionNumber = 33;

### RenderingDefinition

* public RenderMode RenderMode;
        Opaque = 0,
        Cutout = 1,
        Transparent = 2,
        TransparentWithZWrite = 3,
GLTF の alphaMode と 

* public bool TransparentWithZWrite(新規) の２項目を使う

* public CullMode CullMode;
        Off = 0,
        // Front = 1,
        Back = 2,
GLTF の doubleSided を使う

* public int RenderQueueOffsetNumber;

### ColorDefinition

* public Color LitColor;
GLTFのpbrMetallicRoughness/baseColorFactor に格納する

* public Texture2D LitMultiplyTexture;
GLTFのpbrMetallicRoughness/baseColorTexture/index に格納する

* public Color ShadeColor;
* public Texture2D ShadeMultiplyTexture;

* public float CutoutThresholdValue;
GLTFの alphaCutoff に格納する    
    
### LightingDefinition
    
* public LitAndShadeMixingDefinition LitAndShadeMixing;
* public LightingInfluenceDefinition LightingInfluence;
    
### LitAndShadeMixingDefinition

* public float ShadingShiftValue;
* public float ShadingToonyValue;
* public float ShadowReceiveMultiplierValue;
* public Texture2D ShadowReceiveMultiplierMultiplyTexture;
* public float LitAndShadeMixingMultiplierValue;
* public Texture2D LitAndShadeMixingMultiplierMultiplyTexture;

### LightingInfluenceDefinition

* public float LightColorAttenuationValue;
* public float GiIntensityValue;

### EmissionDefinition

* public Color EmissionColor;
GLTFの emissionFactor に格納する

* public Texture2D EmissionMultiplyTexture;
GLTFの emissionMultiplyTexture/index に格納する

### MatCapDefinition

* public Texture2D AdditiveTexture;

### RimDefinition

* public Color RimColor;
* public Texture2D RimMultiplyTexture;
* public float RimLightingMixValue;
* public float RimFresnelPowerValue;
* public float RimLiftValue;

### NormalDefinition

* public Texture2D NormalTexture;
GLTFの normalTexture/index

* public float NormalScaleValue;
GLTFの normalTexture/scale

### OutlineDefinition

* public OutlineWidthMode OutlineWidthMode;
        None = 0,
        WorldCoordinates = 1,
        ScreenCoordinates = 2,

* public float OutlineWidthValue;
* public Texture2D OutlineWidthMultiplyTexture;
* public float OutlineScaledMaxDistanceValue;
* public OutlineColorMode OutlineColorMode;
        FixedColor = 0,
        MixedLighting = 1,

* public Color OutlineColor;
* public float OutlineLightingMixValue;

### TextureUvCoordsDefinition

* public Vector2 MainTextureLeftBottomOriginScale;
* public Vector2 MainTextureLeftBottomOriginOffset;
* public Texture2D UvAnimationMaskTexture;
* public float UvAnimationScrollXSpeedValue;
* public float UvAnimationScrollYSpeedValue;
* public float UvAnimationRotationSpeedValue;

## Extension compatibility and fallback materials

`KHR_materials_unlit` にフォールバックしてください。
