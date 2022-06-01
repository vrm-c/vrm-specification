# VRMC_materials_hdr_emissiveMultiplier

*Version 1.0*

## Contributors

* Sumi Masataka
* Obuchi Yutaka
* Shindo Tetsuro

## Status

Archived

Superseded by [KHR_materials_emissive_strength](https://github.com/KhronosGroup/glTF/blob/main/extensions/2.0/Khronos/KHR_materials_emissive_strength/README.md)

## Dependencies

Written against the glTF 2.0 spec.

## Overview

This extension multiplies and overrides the material's EmissiveFactor value.
You can have an EmissiveFactor with a value greater than 1.

Define it in the material `extensions`.

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

|                    | Type   | Description                     | Required |
|--------------------|--------|---------------------------------|:---------|
| emissiveMultiplier | number | A multiplier for emissiveFactor | ✅        |

Overwrite material.emissiveFactor of the target material with the value multiplied by emissiveMultiplier.
This value is linear.

## Conversion example at the time of export

Convert as follows.

```js
// linear color space
let hdr_emissive_factor = [r, g, b];

let max_component = r;
if (g> max_component)
{
     max_component = g;
}
if (b> max_component)
{
     max_component = b;
}

if (max_component> 1)
{
     // linear color space
     let emissiveFactor = [r / max_component, g / max_component, b / max_component];
     let emissiveMultiplier = max_component;
     // VRMC_materials_hdr_emissiveMultiplier represents an emissive factor value greater than 1.
}
else {
     // linear color space
     let emissiveFactor = [r, g, b];
     let emissiveMultiplier = null;
     // VRMC_materials_hdr_emissiveMultiplier extension not required
}
```
