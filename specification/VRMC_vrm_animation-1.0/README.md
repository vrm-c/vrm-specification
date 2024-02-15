# VRM Animation

*Version 1.0*

## Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [Overview](#overview)
- [Concepts](#concepts)
  - [Animations](#animations)
  - [Humanoid](#humanoid)
  - [Expressions](#expressions)
  - [LookAt](#lookat)
    - [Eye gaze direction](#eye-gaze-direction)
    - [Eye gaze origin position](#eye-gaze-origin-position)
- [glTF Schema Updates](#gltf-schema-updates)
  - [VRMC_vrm_animation](#vrmc_vrm_animation)
    - [Properties](#properties)
    - [JSON Schema](#json-schema)
    - [VRMC_vrm_animation.specVersion ✅](#vrmc_vrm_animationspecversion-)
    - [VRMC_vrm_animation.humanoid](#vrmc_vrm_animationhumanoid)
    - [VRMC_vrm_animation.expressions](#vrmc_vrm_animationexpressions)
    - [VRMC_vrm_animation.lookAt](#vrmc_vrm_animationlookat)
  - [humanoid](#humanoid)
    - [Properties](#properties-1)
    - [JSON Schema](#json-schema-1)
    - [humanoid.humanBones ✅](#humanoidhumanbones-)
  - [humanoid.humanBones](#humanoidhumanbones)
    - [Properties](#properties-2)
    - [JSON Schema](#json-schema-2)
    - [humanoid.humanBones.(Humanoid bone name)](#humanoidhumanboneshumanoid-bone-name)
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
    - [expressions.preset.(preset expression name)](#expressionspresetpreset-expression-name)
  - [expressions.custom](#expressionscustom-1)
    - [Properties](#properties-6)
    - [JSON Schema](#json-schema-6)
    - [expressions.custom.(custom expression name)](#expressionscustomcustom-expression-name)
  - [expressions.expression](#expressionsexpression)
    - [Properties](#properties-7)
    - [JSON Schema](#json-schema-7)
    - [expressions.expression.node ✅](#expressionsexpressionnode-)
  - [lookAt](#lookat)
    - [Properties](#properties-8)
    - [JSON Schema](#json-schema-8)
    - [lookAt.node](#lookatnode)
    - [lookAt.offsetFromHeadBone](#lookatoffsetfromheadbone)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

- Shindo Tetsuro
- 0b5vr

## Status

Complete

## Dependencies

Written against the glTF 2.0 spec.

Also, some definitions in the specifications depend on [`VRMC_vrm`](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/README.md).


## Overview

This specification defines the glTF extension `VRMC_VRM_animation` to describe animation for humanoid models.
The extension is designed to be applied to the model represented with [`VRMC_vrm`](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/README.md) extension, which describes humanoid models.

The hierarchy of the node defined in the glTF is associated with humanoid bones, expressions, and eye gaze, and describes animation that can be applied to any humanoid model.
The actual animation data uses the animation part of the glTF core definition.

It is intended to be used for separate glTF files that only describe animations.
This extension is not expected to be included in VRM models with the `VRMC_vrm` extension.

## Concepts

### Animations

Use animation defined in the glTF core specification.

As a general rule, when using animations, the first animation defined in `animations` is loaded.
A single file may contain multiple animations.
Implementations may support multiple animations contained in a single file.

glTF animations do not have the concept of frame rate because keyframes can be inserted at any time.
However, to ensure that the animation is played back at a reasonable frame rate, we propose a frame rate of 30 frames per second as a guideline that is not too high or too low, taking into account the following points:

* 60 FPS or 30 FPS is common in games
* The raw data (before processing) of motion capture may have a high frame rate
* In video production, there might be 24 FPS, etc.

Considering that the animation is interpolated linearly in the implementation, 30 FPS should be sufficient to ensure smoothness.

### Humanoid

Humanoid defines the humanoid bones.
Each humanoid bone is represented as a node in glTF.

`VRMC_vrm_animation/humanoid` maps the glTF node to the human bone.
The definition of the bone and its structure used in `VRMC_vrm` extension are based on the Humanoid of `VRMC_vrm` extension.

https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/humanoid.md

However, the `leftEye` and `rightEye` of the Humanoid bone cannot have animation data for them.
These are handled by LookAt described later.

> For the future extensibility, the entire hierarchy of the Humanoid bone is included in the animation data.

Nodes specified as Humanoid bones must follow the pose called "VRM T-pose" in the rest pose where no animation is applied.

https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/tpose.md

Animations for nodes specified as Humanoid bones are treated as animation data for the Humanoid bone.
The animation data for the Humanoid bone must not include scales for the Humanoid bone.
Also, the animation for the Humanoid bone must not include translations for bones other than the Hips bone.

> In the implementation, it is desirable to use advanced animation retargeting like Unity's Humanoid system, but if this is difficult, you can simply transfer it as FK animation.

Also, it is strongly recommended that the hierarchy of nodes that represent the `T-Pose` does not include `scale` separately from the animation data.

> This can be resolved by pre-multiplying the scale value to the hierarchy and animation in the application that generates the data.
> This is intended to reduce the burden on the program that loads the runtime.

However, the hierarchy of T-pose can have any rotation.
Please refer to [Pose Data Compatibility](./how_to_transform_human_pose.md) for details.

### Expressions

Expressions defines the expressions.
Each expression has a scalar weight in the range of [0, 1], which indicates how much the expression is expressed.

> In the `VRMC_vrm` extension, the weight of the expression is applied to the mesh morph, the color of the material, and the UV of the texture.

`VRMC_vrm_animation/expressions` maps the glTF node to the expression.
The definition of the expressions is based on the expressions defined in the `VRMC_vrm` extension.

https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/expressions.ja.md

However, `lookUp`, `lookDown`, `lookLeft`, and `lookRight` of the preset expressions cannot have animation data for them.
These are handled by LookAt described later.

Among expressions, preset expressions defined by the `VRMC_vrm` extension are defined as preset expressions, and others are defined as custom expressions.
Custom expressions cannot have the same name as the preset expressions.

Among the animations for the nodes specified as expressions, the X component of the translation is treated as the animation data for the weight of the expression.
The weight value should be in the range of [0, 1].
If the weight value is outside the range of [0, 1], the implementation must clamp the value to the range of [0, 1].

### LookAt

LookAt defines the eye gaze.

> In the `VRMC_vrm` extension, the eye gaze is controlled by the rotation of the humanoid bones and the animation of the expressions.

#### Eye gaze direction

LookAt has an eye gaze direction, which indicates that the model moves its gaze in that direction.

`VRMC_vrm_animation/lookAt/node` specifies the glTF node that has the rotation as the eye gaze direction.
The rotation in the local space of the specified node is treated as the animation data for the eye gaze direction.

In glTF, the rotation is defined as a quaternion.
However, when applying it to the LookAt component of the `VRMC_vrm`, it is converted to the yaw-pitch Euler angle.
The rotation order of the Euler angle must be interpreted as Extrinsic ZXY, and the rotation around the Y axis is yaw and the rotation around the X axis is pitch.

#### Eye gaze origin position

The LookAt defined in this extension defines the eye gaze origin position according to the [LookAt space defined in the `VRMC_vrm` specification](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/lookAt.md#lookat-space-offsetfromheadbone).
The eye gaze origin position is the position translated by `VRMC_vrm_animation/offsetFromHeadBone` in the space relative to the head bone defined in Humanoid.
The initial eye gaze is directed toward the +Z axis in the model space.

> Implementation Note: If you want to use the eye gaze direction as it is without processing the eye gaze direction, you do not need to interpret the value of `offsetFromHeadBone`. If you want to apply some retargeting to the eye gaze according to the bone structure of the destination model, it may be effective to use the eye gaze origin position of the animation model taking into account the value of `offsetFromHeadBone`.

If humanoid is not defined, the value of `offsetFromHeadBone` is interpreted as the eye gaze origin position as it is.

## glTF Schema Updates

The `VRMC_vrm_animation` extension is defined at the root level of glTF.

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
      "specVersion": "1.0",
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
        "node": 64,
        "offsetFromHeadBone": [
          0.0,
          0.06,
          0.0
        ]
      }
    }
  },
  // ...
}
```

### VRMC_vrm_animation

The root object of the extension.

#### Properties

||Type|Description|Required|
|:-|:-|:-|:-|
|`specVersion`|`string`|The spec version of this extension|✅ Yes|
|`humanoid`|`humanoid`|Definition of Humanoid bones|No|
|`expressions`|`expressions`|Definition of expressions|No|
|`lookAt`|`lookAt`|Definition of eye gaze|No|

#### JSON Schema

[VRMC_vrm_animation.schema.json](schema/VRMC_vrm_animation.schema.json)

#### VRMC_vrm_animation.specVersion ✅

The spec version of the `VRMC_vrm_animation` extension.
The value must be `"1.0"`.

- Type: `string`
- Required: Yes

#### VRMC_vrm_animation.humanoid

An object that defines humanoid bones.

- Type: `humanoid`
- Required: No

#### VRMC_vrm_animation.expressions

An object that defines expressions.

- Type: `expressions`
- Required: No

#### VRMC_vrm_animation.lookAt

An object that defines eye gaze.

- Type: `lookAt`
- Required: No

---

### humanoid

An object that defines Humanoid bones.

#### Properties

||Type|Description|Required|
|:-|:-|:-|:-|
|humanBones|`humanoid.humanBones`|Mapping of humanoid bones and nodes|Yes ✅|

#### JSON Schema

[VRMC_vrm_animation.humanoid.schema.json](schema/VRMC_vrm_animation.humanoid.schema.json)

#### humanoid.humanBones ✅

An object that defines the mapping of Humanoid bones and nodes.

- Type: `humanoid.humanBones`
- Required: Yes

### humanoid.humanBones

An object that maps Humanoid bones and nodes.

#### Properties

||Type|Description|Required|
|:-|:-|:-|:-|
|(Humanoid bone name)|`humanoid.humanBones.humanBone`|A humanoid bone|Mixed|

#### JSON Schema

[VRMC_vrm_animation.humanoid.humanBones.schema.json](schema/VRMC_vrm_animation.humanoid.humanBones.schema.json)

#### humanoid.humanBones.(Humanoid bone name)

An object that represents a humanoid bone.
The bone name is the name of the humanoid bone defined by the `VRMC_vrm` extension (`hips`, `leftUpperArm`, etc.)
However, `leftEye` and `rightEye` cannot be defined.

- Type: `humanoid.humanBones.humanBone`
- Required: If the bone is required in `VRMC_vrm` extension, this value is required.

### humanoid.humanBones.humanBone

An object that represents a humanoid bone.

#### Properties

||Type|Description|Required|
|:-|:-|:-|:-|
|`node`|`integer`|The index of the node corresponds to the humanoid bone|✅ Yes|

#### JSON Schema

[VRMC_vrm_animation.humanoid.humanBones.humanBone.schema.json](schema/VRMC_vrm_animation.humanoid.humanBones.humanBone.schema.json)

#### humanoid.humanBones.humanBone.node ✅

The index of the node corresponds to the humanoid bone.

- Type: `integer`
- Required: Yes

---

### expressions

An object that defines expressions.

#### Properties

||Type|Description|Required|
|:-|:-|:-|:-|
|`preset`|`expressions.preset`|Definition for preset expressions|No|
|`custom`|`expressions.custom`|Definition for custom expressions|No|

#### JSON Schema

[VRMC_vrm_animation.expressions.schema.json](schema/VRMC_vrm_animation.expressions.schema.json)

#### expressions.preset

An object that defines preset expressions.

- Type: `expressions.preset`
- Required: No


#### expressions.custom

An object that defines custom expressions.

- Type: `expressions.custom`
- Required: No

### expressions.preset

An object that defines preset expressions.

#### Properties

||Type|Description|Required|
|:-|:-|:-|:-|
|(preset expression name)|`expressions.expression`|A preset expression|No|

#### JSON Schema

[VRMC_vrm_animation.expressions.schema.json](schema/VRMC_vrm_animation.expressions.schema.json)

#### expressions.preset.(preset expression name)

An object that represents a preset expression.
The expression name is one of the names of the preset expression defined in `VRMC_vrm` (`aa`, `leftBlink`, etc.)
However, `lookUp`, `lookDown`, `lookLeft`, and `lookRight` cannot be defined.

- Type: `expressions.expression`
- Required: No

### expressions.custom

An object that defines custom expressions.

#### Properties

||Type|Description|Required|
|:-|:-|:-|:-|
|(custom expression name)|`expressions.expression`|A custom expression|No|

#### JSON Schema

[VRMC_vrm_animation.expressions.schema.json](schema/VRMC_vrm_animation.expressions.schema.json)

#### expressions.custom.(custom expression name)

An object that represents a custom expression.
The expression name can be any name other than the preset expression name defined in `VRMC_vrm` extension.

- Type: `expressions.expression`
- Required: No

### expressions.expression

An object that represents an expression.

#### Properties

||Type|Description|Required|
|:-|:-|:-|:-|
|`node`|`integer`|The index of the node corresponds to the expression|✅ Yes|

#### JSON Schema

[VRMC_vrm_animation.expressions.expression.schema.json](schema/VRMC_vrm_animation.expressions.expression.schema.json)

#### expressions.expression.node ✅

The index of the node corresponds to the expression.

- Type: `integer`
- Required: Yes

---

### lookAt

An object that defines eye gaze.

#### Properties

||Type|Description|Required|
|:-|:-|:-|:-|
|`node`|`integer`|The index of the node corresponds to the eye gaze direction|No|
|`offsetFromHeadBone`|`number[3]`|The offset from the head bone to the eye gaze position|No|

#### JSON Schema

[VRMC_vrm_animation.lookAt.schema.json](schema/VRMC_vrm_animation.lookAt.schema.json)

#### lookAt.node

The index of the node corresponds to the eye gaze direction.

- Type: `integer`
- Required: No

#### lookAt.offsetFromHeadBone

The offset from the head bone to the eye gaze position.

If humanoid is not defined, the value of `offsetFromHeadBone` is interpreted as the eye gaze position as it is.

- Type: `number[3]`
- Required: No
