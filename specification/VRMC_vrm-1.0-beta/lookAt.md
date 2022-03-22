# `VRMC_vrm.lookAt`

This document provides specifications for the `lookAt` field of the` VRMC_vrm` extension.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [LookAt](#lookat)
- [LookAtType](#lookattype)
- [Horizontal Inward / Outward, Vertical Upward / Downward](#horizontal-inward--outward-vertical-upward--downward)
  - [Horizontal Inward Direction](#horizontal-inward-direction)
  - [Horizontal Outward Direction](#horizontal-outward-direction)
  - [Vertical Downward Direction](#vertical-downward-direction)
  - [Vertical Upward Direction](#vertical-upward-direction)
  - [Bone Type](#bone-type)
  - [Expression Type](#expression-type)
- [LookAt Algorithm](#lookat-algorithm)
  - [Bone type](#bone-type)
  - [Expression type](#expression-type)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## LookAt

| Name                    | Note                                                                 |
|:------------------------|:---------------------------------------------------------------------|
| type                    | Bone or Expression                                                   |
| offsetFromHeadBone      | Offset from head bone to reference position of lookAt (between eyes) |
| rangeMapHorizontalInner | The movable range of horizontal inward direction                     |
| rangeMapHorizontalOuter | The movable range of horizontal outward direction                    |
| rangeMapVerticalDown    | The movable range of vertical downward direction                     |
| rangeMapVerticalUp      | The movable range of vertical upward direction                       |

## LookAtType

| Name       | Note                                                                          |
|:-----------|:------------------------------------------------------------------------------|
| bone       | Control the eye gazes with leftEye bone and rightEye bone                     |
| expression | Control the eye gazes with Expression's LookAt, LookDown, LookLeft, LookRight |

expression type can also be set to morph type and UV type

## Reference position

LookAt has a reference position.
This reference position is used for angle calculation of the eye gaze control.
Also, some applications often uses the references position for getting and setting first person view of the model.

The reference position is evaluated using `head` bone of Humanoid and its local coordinate offset `offsetFromHeadBone`.

> Implementation note: If the model does not specify `offsetFromHeadBone`, implementations should fall back to a preferrable value.

## Adjusting the range of movement of the eyes

* rangeMapHorizontalInner
* rangeMapHorizontalOuter
* rangeMapVerticalDown
* rangeMapVerticalUp

You can set the movable range of the eyes with four.

If LookAtType is expression, use the value of `rangeMapHorizontalOuter` for LookLeft, LookRight.
Because LookLeft and LookRight move both eyes together, it is impossible to apply `Inner` to one side and` Outer` to the other side.

> From this, when LookAtType is expression, it is not possible to express cross-eyed eyes.

### Horizontal Inward Direction

`extensions.VRMC_vrm.lookAt.rangeMapHorizontalInner`

* The left eye moves right
* The right eye moves left
* Bone type: outputScale specifies the maximum rotation angle based on the Euler angle (radian) of the leftEye/rightEye bone
* Expression type: outputScale specifies the maximum applicable degree of LookLeft/LookRight Expression (up to 1.0)

```
Y = clamp(yaw, 0, rangeMapHorizontalInner.inputMaxValue)/rangeMapHorizontalInner.inputMaxValue * rangeMapHorizontalInner.outputScale
```

### Horizontal Outward Direction

`extensions.VRMC_vrm.lookAt.rangeMapHorizontalOuter`

* The left eye moves left
* The right eye moves right
* Bone type: outputScale specifies the maximum rotation angle based on the Euler angle (radian) of the leftEye/rightEye bone
* Expression type: outputScale specifies the maximum applicable degree of LookLeft/LookRight Expression (up to 1.0)

```
Y = clamp(yaw, 0, rangeMapHorizontalOuter.inputMaxValue)/rangeMapHorizontalOuter.inputMaxValue * rangeMapHorizontalOuter.outputScale
```

### Vertical Downward Direction

`extensions.VRMC_vrm.lookAt.rangeMapVerticalDown`

* The left eye moves downwards
* The right eye moves downwards
* Bone type: outputScale specifies the maximum rotation angle based on the Euler angle (radian) of the leftEye/rightEye bone
* Expression type: outputScale specifies the maximum applicable degree of LookLeft/LookRight Expression (up to 1.0)

```
Y = clamp(yaw, 0, rangeMapVerticalDown.inputMaxValue)/rangeMapVerticalDown.inputMaxValue * rangeMapVerticalDown.outputScale
```

### Vertical Upward Direction

`extensions.VRMC_vrm.lookAt.rangeMapVerticalUp`

* The left eye moves upwards
* The right eye moves upwards
* Bone type: outputScale specifies the maximum rotation angle based on the Euler angle (radian) of the leftEye/rightEye bone
* Expression type: outputScale specifies the maximum applicable degree of LookLeft/LookRight Expression (up to 1.0)

```
Y = clamp(yaw, 0, rangeMapVerticalUp.inputMaxValue)/rangeMapVerticalUp.inputMaxValue * rangeMapVerticalUp.outputScale
```

### Bone Type

The Yaw and Pitch values (after the adjustment of movable range for eyes) converted to Euler angle are applied to the LocalRotation of the leftEye and rightEye bones, respectively.

### Expression Type

The Yaw and Pitch values (after the adjustment of movable range for eyes) converted to Expression weights are applied to LookLeft, LookRight, LookDown, LookUp Expression, respectively.

## LookAt Algorithm

* The eye gazes are applied to the Yaw/Pitch angle formed by the `HeadBone` (normally it's FirstPersonBone) forward vector and the vector from `HeadBone + firstPersonBoneOffset` to the eye gaze `LookAtObjectPoint`
* x, y = ClampAndScale(yaw, pitch)
* apply(x, y)

### Bone type

| bone and yaw, pitch                | Note                                                     |
|:-----------------------------------|:---------------------------------------------------------|
| leftEye + yaw (left)               | Apply rangeMapHorizontalOuter and reflect as Euler angle |
| leftEye + yaw (right)              | Apply rangeMapHorizontalInner and reflect as Euler angle |
| rightEye + yaw (left)              | Apply rangeMapHorizontalInner and reflect as Euler angle |
| rightEye + yaw (right)             | Apply rangeMapHorizontalOuter and reflect as Euler angle |
| leftEye or rightEye + pitch (down) | Apply rangeMapVerticalDown and reflect as Euler angle    |
| leftEye or rightEye + pitch (up)   | Apply rangeMapVerticalUp and reflect as Euler angle      |

### Expression type

| bone and yaw, pitch               | Note                                                                    |
|:----------------------------------|:------------------------------------------------------------------------|
| leftEye + yaw(left)               | Apply rangeMapHorizontalOuter and reflect as Expression LookLeft value  |
| leftEye + yaw(right)              | Apply rangeMapHorizontalInner and reflect as Expression LookRight value |
| rightEye + yaw(left)              | Apply rangeMapHorizontalInner and reflect as Expression LookLeft value  |
| rightEye + yaw(right)             | Apply rangeMapHorizontalOuter and reflect as Expression LookRight value |
| leftEye or rightEye + pitch(down) | Apply rangeMapVerticalDown and reflect as Expression LookDown value     |
| leftEye or rightEye + pitch(up)   | Apply rangeMapVerticalUp and reflect as Expression LookUp value         |

LookAt Expression has MorphTarget type and TextureUVOffset type. Here the processing is the same regardless of which type is going to be used.

