# `VRMC_vrm.humanoid`

This document provides specifications for the `humanoid` field of the` VRMC_vrm` extension.
Defines a list of `humanoid bone`.

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

* Humanoid bones must be unique in VRM.

### Torso

| Bone Name  | Required | Parent Bone | Estimated Position | Needs Parent Bone |                         Note                          |
| :--------- | :------- | :---------- | :----------------- | :---------------- | :---------------------------------------------------- |
| hips       | Required |             | Crotch             | N/A               | Usually only this bone moves. Other bones rotate only |
| spine      | Required | hips        | Top of pelvis      | N/A               |                                                       |
| chest      |          | spine       | Bottom of rib cage | N/A               | 0.X is required                                       |
| upperChest |          | chest       |                    | Yes               | upperChest can exist only when chest exists           |
| neck       |          | upperChest  | Base of neck       | No                | 0.X is required                                       |

* The bone equivalent to pelvis towards `Y-` direction from hips is deprecated (inverted pelvis).

### Head

| Bone Name | Required | Parent Bone | Estimated Position | Needs Parent Bone |                     Note                     |
| :-------- | :------- | :---------- | :----------------- | :---------------- | :------------------------------------------- |
| head      | Required | neck        | Top of neck        | No                |                                              |
| leftEye   |          | head        |                    | N/A               | The model's eye movement controlled by bones |
| rightEye  |          | head        |                    | N/A               | The model's eye movement controlled by bones |
| jaw       |          | head        |                    | N/A               |                                              |

### Leg

|   Bone Name   | Required |  Parent Bone  | Estimated Position | Needs Parent Bone | Note |
| :------------ | :------- | :------------ | :----------------- | :---------------- | :--- |
| leftUpperLeg  | Required | hips          | groin              | N/A               |      |
| leftLowerLeg  | Required | leftUpperLeg  | knee               | N/A               |      |
| leftFoot      | Required | leftLowerLeg  | ankle              | N/A               |      |
| leftToes      |          | leftFoot      |                    | N/A               |      |
| rightUpperLeg | Required | hips          | groin              | N/A               |      |
| rightLowerLeg | Required | rightUpperLeg | knee               | N/A               |      |
| rightFoot     | Required | rightLowerLeg | ankle              | N/A               |      |
| rightToes     |          | rightFoot     |                    | N/A               |      |

### Arm

|   Bone Name   | Required |  Parent Bone  | Estimated Position | Needs Parent Bone | Note |
| :------------ | :------- | :------------ | :----------------- | :---------------- | :--- |
| leftShoulder  |          | upperChest    |                    | No                |      |
| leftUpperArm  | Required | leftShoulder  | Base of upper arm  | No                |      |
| leftLowerArm  | Required | leftUpperArm  | elbow              | N/A               |      |
| leftHand      | Required | leftLowerArm  | wrist              | N/A               |      |
| rightShoulder |          | upperChest    |                    | No                |      |
| rightUpperArm | Required | rightShoulder | Base of upper arm  | No                |      |
| rightLowerArm | Required | rightUpperArm | elbow              | N/A               |      |
| rightHand     | Required | rightLowerArm | wrist              | N/A               |      |

### Finger

|        Bone Name        | Required |       Parent Bone       | Estimated Position | Needs Parent Bone | Note |
| :---------------------- | :------- | :---------------------- | :----------------- | :---------------- | :--- |
| leftThumbMetacarpal     |          | leftHand                |                    | N/A               |      |
| leftThumbProximal       |          | leftThumbMetacarpal     |                    | Yes               |      |
| leftThumbDistal         |          | leftThumbProximal       |                    | Yes               |      |
| leftIndexProximal       |          | leftHand                |                    | N/A               |      |
| leftIndexIntermediate   |          | leftIndexProximal       |                    | Yes               |      |
| leftIndexDistal         |          | leftIndexIntermediate   |                    | Yes               |      |
| leftMiddleProximal      |          | leftHand                |                    | N/A               |      |
| leftMiddleIntermediate  |          | leftMiddleProximal      |                    | Yes               |      |
| leftMiddleDistal        |          | leftMiddleIntermediate  |                    | Yes               |      |
| leftRingProximal        |          | leftHand                |                    | N/A               |      |
| leftRingIntermediate    |          | leftRingProximal        |                    | Yes               |      |
| leftRingDistal          |          | leftRingIntermediate    |                    | Yes               |      |
| leftLittleProximal      |          | leftHand                |                    | N/A               |      |
| leftLittleIntermediate  |          | leftLittleProximal      |                    | Yes               |      |
| leftLittleDistal        |          | leftLittleIntermediate  |                    | Yes               |      |
| rightThumbMetacarpal    |          | rightHand               |                    | N/A               |      |
| rightThumbProximal      |          | rightThumbMetacarpal    |                    | Yes               |      |
| rightThumbDistal        |          | rightThumbProximal      |                    | Yes               |      |
| rightIndexProximal      |          | rightHand               |                    | N/A               |      |
| rightIndexIntermediate  |          | rightIndexProximal      |                    | Yes               |      |
| rightIndexDistal        |          | rightIndexIntermediate  |                    | Yes               |      |
| rightMiddleProximal     |          | rightHand               |                    | N/A               |      |
| rightMiddleIntermediate |          | rightMiddleProximal     |                    | Yes               |      |
| rightMiddleDistal       |          | rightMiddleIntermediate |                    | Yes               |      |
| rightRingProximal       |          | rightHand               |                    | N/A               |      |
| rightRingIntermediate   |          | rightRingProximal       |                    | Yes               |      |
| rightRingDistal         |          | rightRingIntermediate   |                    | Yes               |      |
| rightLittleProximal     |          | rightHand               |                    | N/A               |      |
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

