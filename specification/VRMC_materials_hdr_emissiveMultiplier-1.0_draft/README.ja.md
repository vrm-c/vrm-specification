# VRMC_materials_hdr_emissiveMultiplier

## Contributors

* 角真宇
* 小渕豊
* 進藤哲郎

## Dependencies

Written against the glTF 2.0 spec.

## Overview

この拡張は、マテリアルの EmissiveFactor の値に乗算してこれを上書きします。
HDR 向けに EmissiveFactor を 1より大きい値に拡張することができます。

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

|                    | 型     | 説明                                        | 必須 |
|--------------------|--------|---------------------------------------------|:-----|
| emissiveMultiplier | number | The HDR emissive multiplier of the material | ✅    |

対象 material の material.emissiveFactor を emissiveMultiplier で乗算した値で上書きします。

## export 時の変換例

以下のように変換してください。

```js
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
    let emissiveFactor = [r/max_component, g/max_component, b/max_component];
    let emissiveMultiplier = max_component;
}
else{
    let emissiveFactor = [r, g, b];
    let emissiveMultiplier = null;
    // VRMC_materials_hdr_emissiveMultiplier 拡張は不要です
}
```
