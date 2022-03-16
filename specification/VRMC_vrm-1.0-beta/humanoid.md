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

| Bone Name  | Required | Parent Bone | Estimated Position | Note                                                  |
|:-----------|:---------|:------------|--------------------|-------------------------------------------------------|
| hips       | Required |             | Crotch             | Usually only this bone moves. Other bones rotate only |
| spine      | Required | hips        | Top of pelvis      |                                                       |
| chest      |          | spine       | Bottom of rib cage | 0.X is required                                       |
| upperChest |          | chest       |                    |                                                       |
| neck       |          | upperChest  | Base of neck       | 0.X is required                                       |

* The bone equivalent to pelvis towards `Y-` direction from hips is deprecated (inverted pelvis).

### Head

| Bone Name | Required | Parent Bone | Estimated Position | Note                                         |
|:----------|:---------|:------------|--------------------|----------------------------------------------|
| head      | Required | neck        | Top of neck        |                                              |
| leftEye   |          | head        |                    | The model's eye movement controlled by bones |
| rightEye  |          | head        |                    | The model's eye movement controlled by bones |
| jaw       |          | head        |                    |                                              |

### Leg

| Bone Name     | Required | Parent Bone   | Estimated Position | Note |
|:--------------|:---------|:--------------|--------------------|------|
| leftUpperLeg  | Required | hips          | groin              |      |
| leftLowerLeg  | Required | leftUpperLeg  | knee               |      |
| leftFoot      | Required | leftLowerLeg  | ankle              |      |
| leftToes      |          | leftFoot      |                    |      |
| rightUpperLeg | Required | hips          | groin              |      |
| rightLowerLeg | Required | rightUpperLeg | knee               |      |
| rightFoot     | Required | rightLowerLeg | ankle              |      |
| rightToes     |          | rightFoot     |                    |      |

### Arm

| Bone Name     | Required | Parent Bone   | Estimated Position | Note |
|:--------------|:---------|:--------------|--------------------|------|
| leftShoulder  |          | chest         |                    |      |
| leftUpperArm  | Required | leftShoulder  | Base of upper arm  |      |
| leftLowerArm  | Required | leftUpperArm  | elbow              |      |
| leftHand      | Required | leftLowerArm  | wrist              |      |
| rightShoulder |          | chest         |                    |      |
| rightUpperArm | Required | rightShoulder | Base of upper arm  |      |
| rightLowerArm | Required | rightUpperArm | elbow              |      |
| rightHand     | Required | rightLowerArm | wrist              |      |

### Finger

| Bone Name               | Required | Parent Bone             | Estimated Position | Note |
|:------------------------|:---------|:------------------------|--------------------|------|
| leftThumbProximal       |          | leftHand                |                    |      |
| leftThumbIntermediate   |          | leftThumbProximal       |                    |      |
| leftThumbDistal         |          | leftThumbIntermediate   |                    |      |
| leftIndexProximal       |          | leftHand                |                    |      |
| leftIndexIntermediate   |          | leftIndexProximal       |                    |      |
| leftIndexDistal         |          | leftIndexIntermediate   |                    |      |
| leftMiddleProximal      |          | leftHand                |                    |      |
| leftMiddleIntermediate  |          | leftMiddleProximal      |                    |      |
| leftMiddleDistal        |          | leftMiddleIntermediate  |                    |      |
| leftRingProximal        |          | leftHand                |                    |      |
| leftRingIntermediate    |          | leftRingProximal        |                    |      |
| leftRingDistal          |          | leftRingIntermediate    |                    |      |
| leftLittleProximal      |          | leftHand                |                    |      |
| leftLittleIntermediate  |          | leftLittleProximal      |                    |      |
| leftLittleDistal        |          | leftLittleIntermediate  |                    |      |
| rightThumbProximal      |          | rightHand               |                    |      |
| rightThumbIntermediate  |          | rightThumbProximal      |                    |      |
| rightThumbDistal        |          | rightThumbIntermediate  |                    |      |
| rightIndexProximal      |          | rightHand               |                    |      |
| rightIndexIntermediate  |          | rightIndexProximal      |                    |      |
| rightIndexDistal        |          | rightIndexIntermediate  |                    |      |
| rightMiddleProximal     |          | rightHand               |                    |      |
| rightMiddleIntermediate |          | rightMiddleProximal     |                    |      |
| rightMiddleDistal       |          | rightMiddleIntermediate |                    |      |
| rightRingProximal       |          | rightHand               |                    |      |
| rightRingIntermediate   |          | rightRingProximal       |                    |      |
| rightRingDistal         |          | rightRingIntermediate   |                    |      |
| rightLittleProximal     |          | rightHand               |                    |      |
| rightLittleIntermediate |          | rightLittleProximal     |                    |      |
| rightLittleDistal       |          | rightLittleIntermediate |                    |      |

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

