# `VRMC_vrm.humanoid`

This document provides specifications for the `humanoid` field of the` VRMC_vrm` extension.
Define a list of `humanoid bone` and `T-Pose`.

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
- [Humanoid T-Pose](#humanoid-t-pose)
  - [Orientation of each bone](#orientation-of-each-bone)
- [Procedure for removing rotation / scale from a node](#procedure-for-removing-rotation--scale-from-a-node)
  - [When rotation / scale is not removed](#when-rotation--scale-is-not-removed)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

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

## Humanoid T-Pose

The VRM requires an initial posture T-Pose.
For T-Pose, follow the following items and the direction of the bending axis of each bone described later.

* Root is the origin
* Grounded at Y = 0
* Pointing in the direction of Z + (Z- direction in 0.X)
* The rotation / scale of each node is cleared.
* Feet, torso and head upright
* Arms horizontal
* Palm facing down

### Orientation of each bone

Make sure that the bending axis of each bone is the specified world axis.

#### Torso

It is upright.

| Bone name  | Bending axis | Remarks |
|:-----------|:-------------|---------|
| hips       |              |         |
| spine      |              |         |
| chest      |              |         |
| upperChest |              |         |
| neck       |              |         |

#### Head

It is facing the front.

| Bone name | Bending axis | Remarks                                       |
|:----------|:-------------|:----------------------------------------------|
| head      |              |                                               |
| leftEye   |              | Make sure your line of sight is on the Z axis |
| rightEye  |              | Make sure your line of sight is on the Z axis |
| jaw       |              |                                               |

#### Legs

It is upright.

| Bone name     | Bending axis | Remarks |
|:--------------|:-------------|:--------|
| leftUpperLeg  |              |         |
| leftLowerLeg  | X-axis       |         |
| leftFoot      | X-axis       |         |
| leftToes      | X-axis       |         |
| rightUpperLeg |              |         |
| rightLowerLeg | X-axis       |         |
| rightFoot     | X-axis       |         |
| rightToes     | X-axis       |         |

#### Arms

Keep your arms (upperArm, lowerArm, hand) horizontal and your palms down.

| Bone name     | Bending axis | Remarks             |
|:--------------|:-------------|:--------------------|
| leftShoulder  |              |                     |
| leftUpperArm  |              |                     |
| leftLowerArm  | Y-axis       |                     |
| leftHand      |              | With your palm down |
| rightShoulder |              |                     |
| rightUpperArm |              |                     |
| rightLowerArm | Y-axis       |                     |
| rightHand     |              | With your palm down |

#### finger

* Make it parallel to the X axis.
* If you tilt your thumb, rotate it in the XY plane.
  * In this case, the bending axis will be perpendicular to the thumb

| Bone name               | Bending axis | Remarks                     |
|:------------------------|:-------------|:----------------------------|
| leftThumbProximal       | Y-axis       | Closest to the index finger |
| leftThumbIntermediate   | Y-axis       | Closest to the index finger |
| leftThumbDistal         | Y-axis       | Closest to the index finger |
| leftIndexProximal       | Z axis       |                             |
| leftIndexIntermediate   | Z axis       |                             |
| leftIndexDistal         | Z axis       |                             |
| leftMiddleProximal      | Z axis       |                             |
| leftMiddleIntermediate  | Z axis       |                             |
| leftMiddleDistal        | Z axis       |                             |
| leftRingProximal        | Z-axis       |                             |
| leftRingIntermediate    | Z axis       |                             |
| leftRingDistal          | Z axis       |                             |
| leftLittleProximal      | Z axis       |                             |
| leftLittleIntermediate  | Z axis       |                             |
| leftLittleDistal        | Z axis       |                             |
| rightThumbProximal      | Y-axis       | Closest to the index finger |
| rightThumbIntermediate  | Y-axis       | Closest to the index finger |
| rightThumbDistal        | Y-axis       | Closest to the index finger |
| rightIndexProximal      | Z-axis       |                             |
| rightIndexIntermediate  | Z-axis       |                             |
| rightIndexDistal        | Z axis       |                             |
| rightMiddleProximal     | Z-axis       |                             |
| rightMiddleIntermediate | Z-axis       |                             |
| rightMiddleDistal       | Z-axis       |                             |
| rightRingProximal       | Z-axis       |                             |
| rightRingIntermediate   | Z-axis       |                             |
| rightRingDistal         | Z-axis       |                             |
| rightLittleProximal     | Z-axis       |                             |
| rightLittleIntermediate | Z-axis       |                             |
| rightLittleDistal       | Z-axis       |                             |

## Procedure for removing rotation / scale from a node

Scene preparation

* Make sure it is the correct size on a metric scale
* Orient the model to Z + at the origin
* Make the model T-Pose

processing

* mesh (without skin)
   * Multiply each vertex by Node.worldMatrix and reuptake as a mesh
* mesh (with skin)
   * If there are vertices without BoneWeight, give BoneWeight to skin.root
   * Skinn the mesh and reuptake the result as a mesh
* Record world coordinates for all nodes
   * In order from the parent, remove the rotation / scale of the node and substitute the recorded world coordinates.
* Update the mesh bind matrix.

`skins [*] .inverseBindMatrices` is

```js
[1, 0, 0, 0]
[0, 1, 0, 0]
[0, 0, 1, 0]
[-x, -y, -z, 1] // World coordinates of x, y, z = joint
```

Is simplified.

### When rotation / scale is not removed

In the base model `inverseBindMatrices`
May include rotation (Z-UP absorption) and scale (such as negative values for mirroring)
This is an application

* Mismatch between bone position and mesh position
* Misjudgment on the front and back of the surface

It causes unexpected results such as, and makes it difficult to control the model.
