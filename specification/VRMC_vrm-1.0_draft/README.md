# VRMC_vrm

## Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [Overview](#overview)
  - [Format and Extension](#format-and-extension)
  - [Version](#version)
  - [Model's Meta Information](#models-meta-information)
  - [Humanoid](#humanoid)
  - [Model Normalization](#model-normalization)
  - [Expression](#expression)
  - [First Person](#first-person)
  - [LookAt](#lookat)
  - [Material](#material)
  - [Constraint](#constraint)
  - [Update Order](#update-order)
- [glTF Schema Updates](#gltf-schema-updates)
  - [Coordinate Units](#coordinate-units)
  - [Unused Items](#unused-items)
  - [Naming Restrictions](#naming-restrictions)
  - [Mesh Storage Restrictions](#mesh-storage-restrictions)
- [JSON Schema](#json-schema)
- [Known Implementations](#known-implementations)
- [Resources](#resources)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

* Shindo Tatsuro
* Hirose Junmichi
* Po-Chang Su

## Status

* In development

## Dependencies

Written against the glTF 2.0 spec.

* Require VRMC_materials_mtoon extension
* require VRMC_springBone extension
* Require KHR_materials_unlit extension
* Require KHR_texture_transform extension

## Overview

Store the information of a humanoid model for VR avatar in the GLTF scene.
By keeping the new information added in the VRM extension while imposing additional constraints on the existing 3D model information in GLTF, humanoid models can be manipulated universally through the program.

## glTF Schema Updates

### Format and Extension

Saved in `.glb` format and used `.vrm` as the extension.

### VRM

The root object of the extension.

#### Properties

| Name        | Value  | Note                             | Required |
|:------------|:-------|:---------------------------------|:---------|
| specVersion | string | Specification version of the VRM | ✅ Yes   |
| meta        | object | Meta informations of the VRM     | ✅ Yes   |

#### vrm.specVersion ✅

Specification version of the VRM.

The version of exporter implementation is output to `assets.generator`.

- Type: `string`
- Required: Yes

#### vrm.meta ✅

A [meta](#meta).

- Type: `object`
- Required: Yes

### Meta

A set of parameters that describes meta information of the model.

#### Properties

| Name               | Value                           | Description                                                                                                      | Required |
|:-------------------|:--------------------------------|:-----------------------------------------------------------------------------------------------------------------|:---------|
| name               | string                          | The name of the model                                                                                      | ✅ Yes   |
| version            | string                          | The version of the model                                                                               | No       |
| authors            | string[]                        | Authors of the model | ✅ Yes   |
| copyrightInformation | string                          | An information that describes the copyright of the model                                                       | No       |
| contactInformation | string                          | An information that describes the contact information of the author                   | No       |
| references         | string[]                        | References / original works of the model             | No       |
| thirdPartyLicenses         | string                        | Third party licenses of the model             | No       |
| thumbnailImage     | integer | The index to access the thumbnail image of the model | No       |
| avatarPermission                 | `OnlyAuthor`, `ExplicitlyLicensedPerson`, `Everyone` | A person who can perform as an avatar with this model                    | No, default: `OnlyAuthor` |
| allowExcessivelyViolentUsage                     | bool                                           | Perform violent acts with this model                        | No, default: `false` |
| allowExcessivelySexualUsage                      | bool                                           | Perform sexual acts with this model                         | No, default: `false` |
| commercialUsage                  | `PersonalNonProfit`, `PersonalProfit`, `Corporation` | Commercial use | No, default: `PersonalNonProfit` |
| allowPoliticalOrReligiousUsage | bool                                           | Permission for political or religious purposes               | No, default: `false` |
| creditNotation | string                                           | An option that forces or abandons to display the credit of this model              | No, default: `required` |
| allowRedistribution               | boolean                                         | A flag that permits to redistribute this model | No |
| modification               | string                                         | An option that controls the condition to modify this model | No, default: `prohibited` |
| otherLicenseUrl               | string                                         | Describe the URL links of other license | No |

#### meta.name ✅

The name of the model.

- Type: `string`
- Required: Yes

#### meta.version

The version of the model.

- Type: `string`
- Required: No

#### meta.authors ✅

Authors of the model.

It must have at least one entry that is not an empty string.
Putting model creator/first author name at the beginning is recommended.

- Type: `string[]`
- Required: Yes

#### meta.copyrightInformation

An information that describes the copyright of the model.

Since the property is intended to be used to display the copyright holder of the model, It must be distinguished from the previous property `authors`.

- Type: `string`
- Required: No

#### meta.contactInformation

An information that describes the contact information of the author.

- Type: `string`
- Required: No

#### meta.references

References / original works of the model.

- Type: `string[]`
- Required: No

#### meta.thirdPartyLicenses

Third party licenses of the model, if required.

You can use line breaks in this property.

- Type: `string`
- Required: No

#### meta.thumbnailImage

The index to access the thumbnail image of the model in gltf.images.

The texture resolution of 1024x1024 is recommended.
It must be square. Preferable resolution is 1024 x 1024.

This is for the application to use as an icon.

- Type: `integer`
- Required: No
- Minimum: `>= 0`

#### meta.avatarPermission

A person who can perform as an avatar with this model.

`onlyAuthor` indicates people cannot use this model as an avatar unless you are the author itself.
`explicitlyLicensedPerson` indicates people who are explicitly licensed can use this model as an avatar. Intended to be used when the model is a paid model, for example.
`everyone` indicates anyone can use this model as an avatar.

- Type: `string`
- Required: No, default: `onlyAuthor`
- Allowed values:
  - `onlyAuthor`
  - `explicitlyLicensedPerson`
  - `everyone`

#### meta.allowExcessivelyViolentUsage

A flag that permits to use this model in excessively violent contents.

- Type: `boolean`
- Required: No, default: `false`

#### meta.allowExcessivelySexualUsage

A flag that permits to use this model in excessively sexual contents.

- Type: `boolean`
- Required: No, default: `false`

#### meta.commercialUsage

An option that permits to use this model in commercial products.

`personalNonProfit` indicates personal users can use this model only if they are not going to make a profit using this model.
`personalProfit` indicates personal users can use this model regardless they are going to make a profit using this model or not.
Corporation users cannot use this model unless the property is `corporation`.

- Type: `string`
- Required: No, default: `personalNonProfit`
- Allowed values:
  - `personalNonProfit`
  - `personalProfit`
  - `corporation`

#### meta.allowPoliticalOrReligiousUsage

A flag that permits to use this model in political or religious contents.

- Type: `boolean`
- Required: No, default: `false`

#### meta.creditNotation

An option that forces or abandons to display the credit of this model.

When the property is `required`, users of the model must show the credit of the model.
When the property is `unnecessary`, users of the model do not have to show the credit of the model.
`abandoned` indicates the holder of the model abandoned their right of the credit.

- Type: `string`
- Required: No, default: `required`
- Allowed values:
  - `required`
  - `unnecessary`
  - `abandoned`

#### meta.allowRedistribution

A flag that permits to redistribute this model.

- Type: `boolean`
- Required: No, default: `false`

#### meta.modification

An option that controls the condition to modify this model.

When the property is `prohibited`, users cannot modify this model.
When the property is either `inherited` or `notInherited`, users can modify this model.
If you modify a model that this property is `inherited`, you must inherit the license condition of the model.

- Type: `string`
- Required: No, default: `prohibited`
- Allowed values:
  - `prohibited`
  - `inherited`
  - `notInherited`

#### meta.otherLicenseUrl

Describe the URL links of other license.

- Type: `string`
- Required: No

### Humanoid

`extensions.VRMC_vrm.humanoid`

VRM defines specifications of the humanoid model.
Here we call the humanoid bone part in the GLTF node hierarchy as Humanoid Skeleton.

#### Humanoid Skeleton Specification

* Each humanoid bone is unique
* All required bones are included
* Inserting non-bone objects between humanoid bones is allowable (e.g., LowerLeg’s parent is an object cube and the cube’s parent is UpperLeg, etc.)
* `Orientation` is the recommended positional relationship for TPose. The same (or near the same) position applied to the parent and child is not recommended as it is likely to cause troubles when judging bone orientations in the application. Please set a valid distance (in floating point) that can separate them

#### Humanoid Bone (enum)

##### Torso (enum)

| Bone Name  | Required | Parent Bone | Orientation | Estimated Position | Note                                                                  |
|:-----------|:---------|:------------|:------------|--------------------|-----------------------------------------------------------------------|
| hips       | Required |             | Y+          | Crotch             | Usually only this bone moves. Other bones rotate only                 |
| spine      | Required | hips        | Y+          | Top of pelvis      |                                                                       |
| chest      |          | spine       | Y+          | Bottom of rib cage | 0.X is required                                                       |
| upperChest |          | chest       | Y+          |                    |                                                                       |
| neck       |          | upperChest  | Y+          | Base of neck       | 0.X is required                                                       |

* The bone equivalent to pelvis towards `Y-` direction from hips is deprecated (inverted pelvis).

##### Head (enum)

| Bone Name | Required | Parent Bone | Orientation | Estimated Position | Note                                         |
|:----------|:---------|:------------|-------------|--------------------|----------------------------------------------|
| head      | Required | neck        |             | Top of neck        |                                              |
| leftEye   |          | head        |             |                    | The model's eye movement controlled by bones |
| rightEye  |          | head        |             |                    | The model's eye movement controlled by bones |
| jaw       |          | head        |             |                    |                                              |

##### Leg (enum)

| Bone Name     | Required | Parent Bone   | Orientation | Estimated Position | Note |
|:--------------|:---------|:--------------|-------------|--------------------|------|
| leftUpperLeg  | Required | hips          | Y-          | groin              |      |
| leftLowerLeg  | Required | leftUpperLeg  | Y-          | knee               |      |
| leftFoot      | Required | leftLowerLeg  | Y-Z+        | ankle              |      |
| leftToes      |          | leftFoot      |             |                    |      |
| rightUpperLeg | Required | hips          |             | groin              |      |
| rightLowerLeg | Required | rightUpperLeg | Y-          | knee               |      |
| rightFoot     | Required | rightLowerLeg | Y-Z+        | ankle              |      |
| rightToes     |          | rightFoot     |             |                    |      |

##### Arm (enum)

| Bone Name     | Required | Parent Bone   | Orientation | Estimated Position | Note |
|:--------------|:---------|:--------------|-------------|--------------------|------|
| leftShoulder  |          | chest         | X-          |                    |      |
| leftUpperArm  | Required | leftShoulder  | X-          | Base of upper arm  |      |
| leftLowerArm  | Required | leftUpperArm  | X-          | elbow              |      |
| leftHand      | Required | leftLowerArm  | X-          | wrist              |      |
| rightShoulder |          | chest         | X+          |                    |      |
| rightUpperArm | Required | rightShoulder | X+          | Base of upper arm  |      |
| rightLowerArm | Required | rightUpperArm | X+          | elbow              |      |
| rightHand     | Required | rightLowerArm | X+          | wrist              |      |

##### Finger (enum)

| Bone Name               | Required | Parent Bone             | Orientation | Estimated Position | Note |
|:------------------------|:---------|:------------------------|-------------|--------------------|------|
| leftThumbProximal       |          | leftHand                | +X+Z        |                    |      |
| leftThumbIntermediate   |          | leftThumbProximal       | +X          |                    |      |
| leftThumbDistal         |          | leftThumbIntermediate   | +X          |                    |      |
| leftIndexProximal       |          | leftHand                | +X          |                    |      |
| leftIndexIntermediate   |          | leftIndexProximal       | +X          |                    |      |
| leftIndexDistal         |          | leftIndexIntermediate   | +X          |                    |      |
| leftMiddleProximal      |          | leftHand                | +X          |                    |      |
| leftMiddleIntermediate  |          | leftMiddleProximal      | +X          |                    |      |
| leftMiddleDistal        |          | leftMiddleIntermediate  | +X          |                    |      |
| leftRingProximal        |          | leftHand                | +X          |                    |      |
| leftRingIntermediate    |          | leftRingProximal        | +X          |                    |      |
| leftRingDistal          |          | leftRingIntermediate    | +X          |                    |      |
| leftLittleProximal      |          | leftHand                | +X          |                    |      |
| leftLittleIntermediate  |          | leftLittleProximal      | +X          |                    |      |
| leftLittleDistal        |          | leftLittleIntermediate  | +X          |                    |      |
| rightThumbProximal      |          | rightHand               | -X          |                    |      |
| rightThumbIntermediate  |          | rightThumbProximal      | -X          |                    |      |
| rightThumbDistal        |          | rightThumbIntermediate  | -X+Z        |                    |      |
| rightIndexProximal      |          | rightHand               | -X          |                    |      |
| rightIndexIntermediate  |          | rightIndexProximal      | -X          |                    |      |
| rightIndexDistal        |          | rightIndexIntermediate  | -X          |                    |      |
| rightMiddleProximal     |          | rightHand               | -X          |                    |      |
| rightMiddleIntermediate |          | rightMiddleProximal     | -X          |                    |      |
| rightMiddleDistal       |          | rightMiddleIntermediate | -X          |                    |      |
| rightRingProximal       |          | rightHand               | -X          |                    |      |
| rightRingIntermediate   |          | rightRingProximal       | -X          |                    |      |
| rightRingDistal         |          | rightRingIntermediate   | -X          |                    |      |
| rightLittleProximal     |          | rightHand               | -X          |                    |      |
| rightLittleIntermediate |          | rightLittleProximal     | -X          |                    |      |
| rightLittleDistal       |          | rightLittleIntermediate | -X          |                    |      |

#### TPose Specification

The skeleton must be the T-pose as the initial posture. 

T-pose's specifications are as follows:

* The root bone (topmost bone, hips' ancestor) is at the origin
* The model's heels touch the ground (Y=0)

| Model's Body Part                                                    | Orientation |
|:---------------------------------------------------------------------|:------------|
| Front                                                                | Z-          |
| Left                                                                 | X+          |
| Up                                                                   | Y+          |
| Torso (hips - spine - chest - upperChest - upperChest - neck - head) | Y+          |
| Left/right leg (upperLeg - lowerLeg)                                 | Y-          |
| Left/right foot                                                      | Y-Z-        |
| Left/eight toe                                                       | Z-          |
| Left arm (shoulder - upperArm - lowerArm - hand) and each finger     | X+          |
| Left thumb (Proximal)                                                | X+Z-        |
| Right arm (shoulder - upperArm - lowerArm - hand) and each finger    | X-          |
| Right thumb (Proximal)                                               | X-Z-        |
| Palm                                                                 | Y-          |

### Model Normalization

An algorithm to constraint on the GLTF part of the VRM model to realize Model Normalization.

#### Node Normalization

The restrictions on VRM's nodes are as follows:

For initial pose (T-Pose):

* No rotation
* No scaling (1, 1, 1)

#### Mesh Normalization

To achieve Node Normalization, the Mesh needs to be normalized.
The normalized Mesh without skinning is restricted by the followings:

* Align with the initial pose of Humanoid Skeleton (if skin.root exists, add with skin.root's coordinate)
* The forward direction is Z-
* The right direction is X+
* The upward direction is Y+

As a result, `skin.inverseBindMatrices` includes only negative translation relative to the target bone in the world coordinate without rotation and scaling.

```js
// Inverse Bind Matrices
// [x, y, z] = bone.translation - skin.translation
[1, 0, 0, 0]
[0, 1, 0, 0]
[0, 0, 1, 0]
[-x,-y,-z, 1]
```

The restrictions on skinning are as follows:

* No skin.skeleton (scene root) or the node at the origin

#### Normalization Algorithm

* Make the model's pose as T-Pose
* Without skin
  * Multiply each vertex by Node.matrix
* With skin
  * If there are vertices without BoneWeight, give skin.root's BoneWeight
  * Skinning the mesh and put in the mesh again as a result (bake, freeze) 
* Remove the Node's rotation/scaling

### Expression

VRM extends MorphTarget for humanoids.
Expression stands for aggregating multiple MorphTarget to specific expressions (Blink, mouth shape AIUEO, Joy, Angry, Sorrow, Fun).
Also, Expression is capable of changing material values (color, texture offset+scale).

#### ExpressionPreset List

##### Facial expression (enum)

| Name    | Note                                                                              |
|:--------|:----------------------------------------------------------------------------------|
| neutral | Standby state. `TODO: Baked as it will be removed from the ExpressionPreset list` |
| joy     |                                                                                   |
| angry   |                                                                                   |
| sorrow  |                                                                                   |
| fun     |                                                                                   |

##### Lip-sync

| Name | Note |
|:-----|:-----|
| aa   | あ   |
| ih   | い   |
| ou   | う   |
| ee   | え   |
| oh   | お   |

##### Blink

| Name       | Note                 |
|:-----------|:---------------------|
| blink      | Blink with both eyes |
| blinkLeft  | Blink with left eye  |
| blinkRight | Blink with right eye |

##### Expression LookAt

| Name      | Note                                                                                           |
|:----------|:-----------------------------------------------------------------------------------------------|
| lookUp    | For models' eye movement controlled by Expression, not Bone. More details in [LookAt](#lookat) |
| lookDown  | For models' eye movement controlled by Expression, not Bone. More details in [LookAt](#lookat) |
| lookLeft  | For models' eye movement controlled by Expression, not Bone. More details in [LookAt](#lookat) |
| lookRight | For models' eye movement controlled by Expression, not Bone. More details in [LookAt](#lookat) |

##### User Definition

| Name   | Note                                                                                        |
|:-------|:--------------------------------------------------------------------------------------------|
| custom | Customized blend shapes used for creators' applications. Please use name for identification |

#### Expression Specification

`extensions.VRMC_vrm.expressions`

| Name                             | Note                                                                                                       |
|:---------------------------------|:-----------------------------------------------------------------------------------------------------------|
| expressions[*].preset       |                                                                                                |
| expressions[*].name         |                                   |
| expressions[*].is_binary    | In the case of `True`: value!=0 will be considered as 1                                                         |
| expressions[*].morphTargetBinds| MorphTargetBind list (described later)                                                                       |
| expressions[*].materialColorBinds    | MaterialValueBind list (described later)                                                               |
| expressions[*].textureTransformBinds| TextureTransformBind                                                                     |
| expressions[*].ignoreBlink  | Force the weight of blink, blink_L, blink_R to become 0 if the weight of this Expression is not 0               |
| expressions[*].ignoreLookAt | Force the weight of lookUp, lookDown, lookLeft, lookRight to become 0 if the weight of this Expression is not 0 |
| expressions[*].ignoreMouth  | Force the weight of A, I, U, E, Oto become 0 if the weight of this Expression is not 0                          |

##### MorphTargetBind

`extensions.VRMC_vrm.expressions[*].morphTargetBinds[*]`

Bind Expression with MorphTarget.

| Name   | Note                                                                                                               |
|:-------|:-------------------------------------------------------------------------------------------------------------------|
| node   | Index of target node that has a mesh                                                                               |
| index  | Index of target morph (All primitives have the same morph [Mesh Storage Restrictions](#mesh-storage-restrictions)) |
| weight | Applied morph value                                                                                                |

##### MaterialColorBind

`extensions.VRMC_vrm.expressions[*].materialColorBinds[*]`

Bind Expression with Material changes.

| Name        | Note                                                     |
|:------------|:---------------------------------------------------------|
| material    | Index of the target material                             |
| type        | Target material's change type (color, uvScale, uvOffset) |
| targetValue | Applied material value (float4)                          |

`extensions.VRMC_vrm.expressions[*].materialColorBinds[*].type`

Each corresponds to following:

| Name          | `pbrMetallicRoughness`                 | `KHR_materials_unlit`                  | `VRMC_materials_mtoon`                          |
|:--------------|:---------------------------------------|:---------------------------------------|:------------------------------------------------|
| color         | `pbrMetallicRoughness.baseColorFactor` | `pbrMetallicRoughness.baseColorFactor` | `pbrMetallicRoughness.baseColorFactor`          |
| emissionColor | `emissiveFactor`                       | Unused                                 | `emissiveFactor`                                |
| shadeColor    | Unused                                 | Unused                                 | `extensions.VRMC_materials_mtoon.shadeFactor`   |
| rimColor      | Unused                                 | Unused                                 | `extensions.VRMC_materials_mtoon.rimFactor`     |
| outlineColor  | Unused                                 | Unused                                 | `extensions.VRMC_materials_mtoon.outlineFactor` |

##### TextureTransformBind

`extensions.VRMC_vrm.expressions[*].textureTransformBinds[*]`

Bind Expression with UV(TEXCOORD_0) changes of the target Material.

| Name        | Note                                          |
|:------------|:----------------------------------------------|
| material    | Index of the target material                  |
| scale       | Applied scale value (float2, default=[1, 1])  |
| offset      | Applied offset value (float2)                 |

#### Expression Update Algorithm

##### Expression Identification

* If preset is not custom, use preset for identification
* If preset is custom, use name for identification

##### MorphTarget

* Set all the morph targets to 0
* Accumulate the values (Weight) of applied blend shapes `void AccumulateValue(Expression expression, float value)`
* The accumulated values are all applied at once

##### Material Value and UVScale Value

* Set all the materials to their initial states (not 0)
* Accumulate the values (Weight) of applied blend shapes `void AccumulateValue(Expression expression, float value)`
* The accumulated values are all applied at once `Base + (A.Target - Base) * A.Weight + (B.Target - Base) * B.Weight`

### First Person

VRM defines first-person view settings for VR.
lookAt.offsetFromHeadBone can be used as a reference position for the HMD.

#### MeshAnnotation

Control renderings in Mesh units.

`extensions.VRMC_vrm.firstPerson.meshAnnotations[*]`

| Name            | Note                                  |
|:----------------|:--------------------------------------|
| node            | Index for target node that has a mesh |
| firstPersonFlag | Described below                       |

firstPersonFlag. When using a model in the VR application, the renderings from the HMD and the others are separated.

#### MeshAnnotation (enum)

`thirdPersonOnly` is for hiding the rendering of the model's head portion in VR. Since we assumed that the VR camera's viewpoint is at the head position, by setting thirdPersonOnly, the situation where the user may inadvertently see the model's face, head and hair (block the first-person view) can be prevented.

`firstPersonOnly` is a setting that objects rendered by the first-person camera can only be seen from the first-person view. Normally `firstPersonOnly` will not be used unless the application side needs it for special purposes.

Set `both` for objects that do not necessarily being rendered separately so that those objects can be rendered by all the cameras.

`auto` is used to divide the mesh into the head portion (`thirdPersonOnly`) and the others (`both`). See the details in the next section.

| Name            | First Person Camera (VR viewpoint) | Other Cameras     | Example                                                                      |
|:----------------|:-----------------------------------|:------------------|------------------------------------------------------------------------------|
| thirdPersonOnly | Rendering disable                  | Rendering enable  | Objects that block VR view such as face, eyes, head, hair, hat, helmet, etc. |
| firstPersonOnly | Rendering enable                   | Rendering disable | User interface that is only visible from the user's perspective view         |
| both            | Rendering enable                   | Rendering enable  |                                                                              |

#### MeshAnnotation.Auto Algorithm

* For a mesh specified as `auto`, each vertex will be checked whether it contains the weight of Head bone (or the child of the Head bone)
* Split the mesh into two groups: the first group contains triangles with HeadBone-related vertices while the second group contains triangles with the rest of the vertices
* Set the mesh containing the HeadBone-related vertices as `thirdPersonOnly`. And another mesh is set as `both`

### LookAt

`extensions.VRMC_vrm.lookAt`

VRM defines eye gaze control for Humanoid.

| Name               | Note                                                                 |
|:-------------------|:---------------------------------------------------------------------|
| lookAtType         | Bone or Expression                                                   |
| offsetFromHeadBone | Offset from head bone to reference position of lookAt (between eyes) |
| horizontalInner    | The movable range of horizontal inward direction                     |
| horizontalOuter    | The movable range of horizontal outward direction                    |
| verticalDown       | The movable range of vertical downward direction                     |
| verticalUp         | The movable range of vertical upward direction                       |

#### LookAtType

| Name       | Note                                                                          |
|:-----------|:------------------------------------------------------------------------------|
| bone       | Control the eye gazes with leftEye bone and rightEye bone                     |
| expression | Control the eye gazes with Expression's LookAt, LookDown, LookLeft, LookRight |

expression type can also be set to morph type and UV type

#### Horizontal Inward / Outward, Vertical Upward / Downward

Adjust the movable range for the eyes.

##### Horizontal Inward Direction

`extensions.VRMC_vrm.lookAt.horizontalInner`

* The left eye moves right
* The right eye moves left
* Bone type: outputScale specifies the maximum rotation angle based on the Euler angle (radian) of the leftEye/rightEye bone
* Expression type: outputScale specifies the maximum applicable degree of LookLeft/LookRight Expression (up to 1.0)

```
Y = clamp(yaw, 0, horizontalInner.inputMaxValue)/horizontalInner.inputMaxValue * horizontalInner.outputScale 
```

##### Horizontal Outward Direction

`extensions.VRMC_vrm.lookAt.horizontalOuter`

* The left eye moves left
* The right eye moves right
* Bone type: outputScale specifies the maximum rotation angle based on the Euler angle (radian) of the leftEye/rightEye bone
* Expression type: outputScale specifies the maximum applicable degree of LookLeft/LookRight Expression (up to 1.0)

```
Y = clamp(yaw, 0, horizontalOuter.inputMaxValue)/horizontalOuter.inputMaxValue * horizontalOuter.outputScale 
```

##### Vertical Downward Direction

`extensions.VRMC_vrm.lookAt.verticalDown`

* The left eye moves downwards
* The right eye moves downwards
* Bone type: outputScale specifies the maximum rotation angle based on the Euler angle (radian) of the leftEye/rightEye bone
* Expression type: outputScale specifies the maximum applicable degree of LookLeft/LookRight Expression (up to 1.0)

```
Y = clamp(yaw, 0, verticalDown.inputMaxValue)/verticalDown.inputMaxValue * verticalDown.outputScale 
```

##### Vertical Upward Direction

`extensions.VRMC_vrm.lookAt.verticalUp`

* The left eye moves upwards
* The right eye moves upwards
* Bone type: outputScale specifies the maximum rotation angle based on the Euler angle (radian) of the leftEye/rightEye bone
* Expression type: outputScale specifies the maximum applicable degree of LookLeft/LookRight Expression (up to 1.0)

```
Y = clamp(yaw, 0, verticalUp.inputMaxValue)/verticalUp.inputMaxValue * verticalUp.outputScale 
```

##### Bone Type

The Yaw and Pitch values (after the adjustment of movable range for eyes) converted to Euler angle are applied to the LocalRotation of the leftEye and rightEye bones, respectively.

##### Expression Type

The Yaw and Pitch values (after the adjustment of movable range for eyes) converted to Expression weights are applied to LookLeft, LookRight, LookDown, LookUp Expression, respectively.

#### LookAt Algorithm

* The eye gazes are applied to the Yaw/Pitch angle formed by the `HeadBone` (normally it's FirstPersonBone) forward vector and the vector from `HeadBone + firstPersonBoneOffset` to the eye gaze `LookAtObjectPoint`
* x, y = ClampAndScale(yaw, pitch)
* apply(x, y)

##### Bone type

| bone and yaw, pitch                | Note                                             |
|:-----------------------------------|:-------------------------------------------------|
| leftEye + yaw (left)               | Apply horizontalOuter and reflect as Euler angle |
| leftEye + yaw (right)              | Apply horizontalInner and reflect as Euler angle |
| rightEye + yaw (left)              | Apply horizontalInner and reflect as Euler angle |
| rightEye + yaw (right)             | Apply horizontalOuter and reflect as Euler angle |
| leftEye or rightEye + pitch (down) | Apply verticalDown and reflect as Euler angle    |
| leftEye or rightEye + pitch (up)   | Apply verticalUp and reflect as Euler angle      |

##### Expression type

| bone and yaw, pitch               | Note                                                            |
|:----------------------------------|:----------------------------------------------------------------|
| leftEye + yaw(left)               | Apply horizontalOuter and reflect as Expression LookLeft value  |
| leftEye + yaw(right)              | Apply horizontalInner and reflect as Expression LookRight value |
| rightEye + yaw(left)              | Apply horizontalInner and reflect as Expression LookLeft value  |
| rightEye + yaw(right)             | Apply horizontalOuter and reflect as Expression LookRight value |
| leftEye or rightEye + pitch(down) | Apply verticalDown and reflect as Expression LookDown value     |
| leftEye or rightEye + pitch(up)   | Apply verticalUp and reflect as Expression LookUp value         |

LookAt Expression has MorphTarget type and TextureUVOffset type. Here the processing is the same regardless of which type is going to be used.

### Material

Dependencies.

#### VRMC_materials_mtoon extension

Define Toon Shader.

#### KHR_materials_unlit extension

UNLIT is required.

#### KHR_texture_transform extension

Limited usage.

Use

`materials[*].pbrMetallicRoughness.baseColorTexture.extensions.KHR_texture_transform`

Other textures that access `uv` also refer to this value.Expression の 
Limited by TextureTransformBind.

### Constraint

TODO:

### Update Order

The followings are the recommended update order:

1. Resolve Humanoid Bone
2. Since the head position has been determined, LookAt can be resolved
  * Bone type
  * Expression type
3. Update Expression
  * LipSync
  * AutoBlink
  * Expression type of LookAt
  * External inputs such as controller
4. Apply Expression
5. Resolve constraints
6. Resolve SpringBone

## glTF Schema Updates

### Coordinate Units

Metric units based on glTF [coordinate-system-and-units](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#coordinate-system-and-units).

### Unused Items

The following items are not used:

* animations
* cameras

### Naming Restrictions

* Make all names unique

### Mesh Storage Restrictions

#### not store TANGENT

* TANGENT information is not saved in the exported VRM model. If NormalMap exists, TANGENT can be obtained via MIKK method
* meshes[*].primitives[*].attributes.TANGENT
* meshes[*].primitives[*].targets.TANGENT

#### VertexBuffer Restrictions

Given an input mesh, each primitive comprised by different buffers is prohibited

* Each primitive must have the same attributes

Each primitive is not allowed to have independent morph.
It is assumed that morph target is set for mesh as opposed to primitive.

* Each primitive must have the same target
* Each target must have the same attributes

##### VertexBuffer Layout

```
primitives[*].attributes (each primitive uses a unique accessor)
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

* Make all the primitives have the same attributes
  * Example: `primitive[0]`(POSITION, NORMAL, TEXCOORD_0) may have a different structure from `primitive[1]`(POSITION)
* Same as primitive.targets

```
NG

primitives[*].attributes (each primitive uses a unique accessor)
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
      // The information of VRM extension
      "specVersion": "1.0"
    }
  },
  // The information of general GLTF-2.0
}
```

* https://github.com/vrm-c/vrm-specification/tree/master/specification/VRMC_vrm-1.0_draft/schema

GLTF-2.0 JsonSchema.

* https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/schema

## Known Implementations

https://vrm.dev/en/vrm_applications/

## Resources

* https://vrm.dev/en/
