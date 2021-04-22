# `VRMC_vrm.humanoid`

本文書では、 `VRMC_vrm` 拡張のうち `humanoid` フィールドについての仕様を示します。

```json
extensions.VRMC_vrm.humanoid.humanBones = {
  "hips": {
    "node": 1 // index of glTF.Node
  },
  // 省略
}
```

## ヒューマノイドボーンの一覧

### 胴

| ボーン名前 | 必須 | 親ボーン   | 位置の目安 | 備考                                                     |
|:-----------|:-----|:-----------|------------|----------------------------------------------------------|
| hips       | 必須 | (root)     | 股間       | 通常、このボーンだけが移動し、他のボーンは回転だけします |
| spine      | 必須 | hips       | 骨盤の上端 |                                                          |
| chest      |      | spine      | 胸郭の下端 | 0.X では必須だった                                       |
| upperChest |      | chest      |            |                                                          |
| neck       |      | upperChest | 首の付け根 | 0.X では必須だった                                       |

### 頭

| ボーン名前 | 必須 | 親ボーン | 位置の目安 | 備考                                                                                                                                        |
|:-----------|:-----|:---------|------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| head       | 必須 | neck     | 首の上端   |                                                                                                                                             |
| leftEye    |      | head     |            | [`VRMC_vrm.lookAt` 視線制御(オプション)](#vrmc_vrmlookat-%E8%A6%96%E7%B7%9A%E5%88%B6%E5%BE%A1%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3) |
| rightEye   |      | head     |            | [`VRMC_vrm.lookAt` 視線制御(オプション)](#vrmc_vrmlookat-%E8%A6%96%E7%B7%9A%E5%88%B6%E5%BE%A1%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3) |
| jaw        |      | head     |            |                                                                                                                                             |

### 脚

| ボーン名前    | 必須 | 親ボーン      | 位置の目安     | 備考 |
|:--------------|:-----|:--------------|----------------|------|
| leftUpperLeg  | 必須 | hips          | 脚の付け根     |      |
| leftLowerLeg  | 必須 | leftUpperLeg  | 膝             |      |
| leftFoot      | 必須 | leftLowerLeg  | 足首           |      |
| leftToes      |      | leftFoot      | 足の指の付け根 |      |
| rightUpperLeg | 必須 | hips          | 脚の付け根     |      |
| rightLowerLeg | 必須 | rightUpperLeg | 膝             |      |
| rightFoot     | 必須 | rightLowerLeg | 足首           |      |
| rightToes     |      | rightFoot     | 足の指の付け根 |      |

### 腕

| ボーン名前    | 必須 | 親ボーン      | 位置の目安   | 備考 |
|:--------------|:-----|:--------------|--------------|------|
| leftShoulder  |      | chest         |              |      |
| leftUpperArm  | 必須 | leftShoulder  | 上腕の付け根 |      |
| leftLowerArm  | 必須 | leftUpperArm  | 肘           |      |
| leftHand      | 必須 | leftLowerArm  | 手首         |      |
| rightShoulder |      | chest         |              |      |
| rightUpperArm | 必須 | rightShoulder | 上腕の付け根 |      |
| rightLowerArm | 必須 | rightUpperArm | 肘           |      |
| rightHand     | 必須 | rightLowerArm | 手首         |      |

### 指

| ボーン名前              | 必須 | 親ボーン                | 位置の目安 | 備考 |
|:------------------------|:-----|:------------------------|------------|------|
| leftThumbProximal       |      | leftHand                |            |      |
| leftThumbIntermediate   |      | leftThumbProximal       |            |      |
| leftThumbDistal         |      | leftThumbIntermediate   |            |      |
| leftIndexProximal       |      | leftHand                |            |      |
| leftIndexIntermediate   |      | leftIndexProximal       |            |      |
| leftIndexDistal         |      | leftIndexIntermediate   |            |      |
| leftMiddleProximal      |      | leftHand                |            |      |
| leftMiddleIntermediate  |      | leftMiddleProximal      |            |      |
| leftMiddleDistal        |      | leftMiddleIntermediate  |            |      |
| leftRingProximal        |      | leftHand                |            |      |
| leftRingIntermediate    |      | leftRingProximal        |            |      |
| leftRingDistal          |      | leftRingIntermediate    |            |      |
| leftLittleProximal      |      | leftHand                |            |      |
| leftLittleIntermediate  |      | leftLittleProximal      |            |      |
| leftLittleDistal        |      | leftLittleIntermediate  |            |      |
| rightThumbProximal      |      | rightHand               |            |      |
| rightThumbIntermediate  |      | rightThumbProximal      |            |      |
| rightThumbDistal        |      | rightThumbIntermediate  |            |      |
| rightIndexProximal      |      | rightHand               |            |      |
| rightIndexIntermediate  |      | rightIndexProximal      |            |      |
| rightIndexDistal        |      | rightIndexIntermediate  |            |      |
| rightMiddleProximal     |      | rightHand               |            |      |
| rightMiddleIntermediate |      | rightMiddleProximal     |            |      |
| rightMiddleDistal       |      | rightMiddleIntermediate |            |      |
| rightRingProximal       |      | rightHand               |            |      |
| rightRingIntermediate   |      | rightRingProximal       |            |      |
| rightRingDistal         |      | rightRingIntermediate   |            |      |
| rightLittleProximal     |      | rightHand               |            |      |
| rightLittleIntermediate |      | rightLittleProximal     |            |      |
| rightLittleDistal       |      | rightLittleIntermediate |            |      |

## ボーンの親子関係

* ボーンの間にボーンではないノードが入ることは許容します(UpperLegとLowerLegの間にボーンでないノードがあるなど)
