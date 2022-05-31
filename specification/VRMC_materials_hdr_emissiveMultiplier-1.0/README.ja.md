# VRMC_materials_hdr_emissiveMultiplier

## Contributors

* 角真宇
* 小渕豊
* 進藤哲郎

## Status

Archived

Superseded by [KHR_materials_emissive_strength](https://github.com/KhronosGroup/glTF/blob/main/extensions/2.0/Khronos/KHR_materials_emissive_strength/README.md)

## Dependencies

Written against the glTF 2.0 spec.

## Overview

この拡張は、マテリアルの EmissiveFactor の値に乗算してこれを上書きします。
1 よりも大きい値の EmissiveFactor を持つことができます。

マテリアルの `extensions` に定義します。

```json
{
    "materials": [
        {
            "name": "MyPBRMaterial",
            // emission
            "emissiveTexture": {
            },
            "emissiveFactor": [1, 1, 1],
            // extension
            "extensions": {
                "VRMC_materials_hdr_emissiveMultiplier": {
                    "emissiveMultiplier": 2.0,
                }
            }
        }
    ]
}
```

## Defined Properties

|                    | 型     | 説明                            | 必須 |
|--------------------|--------|---------------------------------|:-----|
| emissiveMultiplier | number | A multiplier for emissiveFactor | ✅    |

対象 material の material.emissiveFactor を emissiveMultiplier で乗算した値で上書きします。
この値はリニアです。

## export 時の変換例

以下のように変換してください。

```js
// linear color space
let hdr_emissive_factor = [r, g, b];

let max_component = r;
if(g>max_component)
{
    max_component = g;
}
if(b>max_component)
{
    max_component = b;
}

if(max_component>1)
{
    // linear color space
    let emissiveFactor = [r/max_component, g/max_component, b/max_component];
    let emissiveMultiplier = max_component;
    // VRMC_materials_hdr_emissiveMultiplier により1を越える emissive factor 値を表します
}
else{
    // linear color space
    let emissiveFactor = [r, g, b];
    let emissiveMultiplier = null;
    // VRMC_materials_hdr_emissiveMultiplier 拡張は不要です
}
```
