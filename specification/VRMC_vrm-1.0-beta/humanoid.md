# `VRMC_vrm.humanoid`

This document provides specifications for the `humanoid` field of the` VRMC_vrm` extension.
Define a list of `humanoid bone`.

```json
extensions.VRMC_vrm.humanoid = {
  "humanBones": {
    "hips": {
      "node": 1 // index of glTF.Node
    },
    "spine": {
      "node": 2
    },
    //
  }
}
```

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [List of humanoid bones](#list-of-humanoid-bones)
  - [Torso](#torso)
  - [Head](#head)
  - [Leg](#leg)
  - [Arm](#arm)
  - [Finger](#finger)
- [Humanoid bone parent-child relationship](#humanoid-bone-parent-child-relationship)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Forbidden Transforms

For transforms of humanoid bones, scale components MUST have positive values (zero is not permitted).

## List of humanoid bones

* Humanoid bones must unique in vrm.

### Torso

| Bone Name  | Required | Parent Bone | Estimated Position | Needs Parent Bone | Note                                                  |
|:-----------|:---------|:------------|:-------------------|:------------------|:------------------------------------------------------|
| hips       | Required |             | Crotch             |                   | Usually only this bone moves. Other bones rotate only |
| spine      | Required | hips        | Top of pelvis      |                   |                                                       |
| chest      |          | spine       | Bottom of rib cage |                   | 0.X is required                                       |
| upperChest |          | chest       |                    | Yes               | upperChest can exist only when chest exists           |
| neck       |          | upperChest  | Base of neck       |                   | 0.X is required                                       |

* The bone equivalent to pelvis towards `Y-` direction from hips is deprecated (inverted pelvis).

### Head

| Bone Name | Required | Parent Bone | Estimated Position | Needs Parent Bone | Note                                         |
|:----------|:---------|:------------|:-------------------|:------------------|:---------------------------------------------|
| head      | Required | neck        | Top of neck        |                   |                                              |
| leftEye   |          | head        |                    |                   | The model's eye movement controlled by bones |
| rightEye  |          | head        |                    |                   | The model's eye movement controlled by bones |
| jaw       |          | head        |                    |                   |                                              |

### Leg

| Bone Name     | Required | Parent Bone   | Estimated Position | Needs Parent Bone | Note |
|:--------------|:---------|:--------------|:-------------------|:------------------|:-----|
| leftUpperLeg  | Required | hips          | groin              |                   |      |
| leftLowerLeg  | Required | leftUpperLeg  | knee               |                   |      |
| leftFoot      | Required | leftLowerLeg  | ankle              |                   |      |
| leftToes      |          | leftFoot      |                    |                   |      |
| rightUpperLeg | Required | hips          | groin              |                   |      |
| rightLowerLeg | Required | rightUpperLeg | knee               |                   |      |
| rightFoot     | Required | rightLowerLeg | ankle              |                   |      |
| rightToes     |          | rightFoot     |                    |                   |      |

### Arm

| Bone Name     | Required | Parent Bone   | Estimated Position | Needs Parent Bone | Note |
|:--------------|:---------|:--------------|:-------------------|:------------------|:-----|
| leftShoulder  |          | chest         |                    |                   |      |
| leftUpperArm  | Required | leftShoulder  | Base of upper arm  |                   |      |
| leftLowerArm  | Required | leftUpperArm  | elbow              |                   |      |
| leftHand      | Required | leftLowerArm  | wrist              |                   |      |
| rightShoulder |          | chest         |                    |                   |      |
| rightUpperArm | Required | rightShoulder | Base of upper arm  |                   |      |
| rightLowerArm | Required | rightUpperArm | elbow              |                   |      |
| rightHand     | Required | rightLowerArm | wrist              |                   |      |

### Finger

| Bone Name               | Required | Parent Bone             | Estimated Position | Needs Parent Bone | Note |
|:------------------------|:---------|:------------------------|:-------------------|:------------------|:-----|
| leftThumbMetacarpal     |          | leftHand                |                    |                   |      |
| leftThumbProximal       |          | leftThumbMetacarpal     |                    | Yes               |      |
| leftThumbDistal         |          | leftThumbProximal       |                    | Yes               |      |
| leftIndexProximal       |          | leftHand                |                    |                   |      |
| leftIndexIntermediate   |          | leftIndexProximal       |                    | Yes               |      |
| leftIndexDistal         |          | leftIndexIntermediate   |                    | Yes               |      |
| leftMiddleProximal      |          | leftHand                |                    |                   |      |
| leftMiddleIntermediate  |          | leftMiddleProximal      |                    | Yes               |      |
| leftMiddleDistal        |          | leftMiddleIntermediate  |                    | Yes               |      |
| leftRingProximal        |          | leftHand                |                    |                   |      |
| leftRingIntermediate    |          | leftRingProximal        |                    | Yes               |      |
| leftRingDistal          |          | leftRingIntermediate    |                    | Yes               |      |
| leftLittleProximal      |          | leftHand                |                    |                   |      |
| leftLittleIntermediate  |          | leftLittleProximal      |                    | Yes               |      |
| leftLittleDistal        |          | leftLittleIntermediate  |                    | Yes               |      |
| rightThumbMetacarpal    |          | rightHand               |                    |                   |      |
| rightThumbProximal      |          | rightThumbMetacarpal    |                    | Yes               |      |
| rightThumbDistal        |          | rightThumbProximal      |                    | Yes               |      |
| rightIndexProximal      |          | rightHand               |                    |                   |      |
| rightIndexIntermediate  |          | rightIndexProximal      |                    | Yes               |      |
| rightIndexDistal        |          | rightIndexIntermediate  |                    | Yes               |      |
| rightMiddleProximal     |          | rightHand               |                    |                   |      |
| rightMiddleIntermediate |          | rightMiddleProximal     |                    | Yes               |      |
| rightMiddleDistal       |          | rightMiddleIntermediate |                    | Yes               |      |
| rightRingProximal       |          | rightHand               |                    |                   |      |
| rightRingIntermediate   |          | rightRingProximal       |                    | Yes               |      |
| rightRingDistal         |          | rightRingIntermediate   |                    | Yes               |      |
| rightLittleProximal     |          | rightHand               |                    |                   |      |
| rightLittleIntermediate |          | rightLittleProximal     |                    | Yes               |      |
| rightLittleDistal       |          | rightLittleIntermediate |                    | Yes               |      |

## Humanoid bone parent-child relationship

* The parent bone of the humanoid bone is fixed. If no non-essential parent bone exists, look for the parent bone of the parent bone.
* It is permissible to have non-humanoid bone nodes between humanoid bones (for example, there are non-humanoid bone nodes between Upper Leg and Lower Leg).

With hips as root, the parent and child are as follows.

* root (not a humanoid bone, origin)
  * hips
    * spine
      * (chest)
        * (upperChest)
          * (neck)
            * head
              * (leftEye)
              * (rightEye)
              * (jaw)
          * (leftShoulder)
            * leftUpperArm
              * leftLowerArm
                * leftHand
                  * (fingers)...
          * (rightShoulder)
            * rightUpperArm
              * rightLowerArm
                * rightHand
                  * (fingers)...
    * leftUpperLeg
      * leftLowerLeg
        * leftFoot
          * (leftToes)
    * rightUpperLeg
      * rightLowerLeg
        * rightFoot
          * (leftToes)

