# `VRMC_vrm.humanoid`

本文書では、 `VRMC_vrm` 拡張のうち `humanoid` フィールドについての仕様を示します。
`humanoid bone` の一覧を定義します。

```json
extensions.VRMC_vrm.humanoid = {
  "humanBones": {
    "hips": {
      "node": 1 // index of glTF.Node
    },
    "spine": {
      "node": 2
    },
    // 省略
  }
}
```

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [ヒューマノイドボーンの一覧](#%E3%83%92%E3%83%A5%E3%83%BC%E3%83%9E%E3%83%8E%E3%82%A4%E3%83%89%E3%83%9C%E3%83%BC%E3%83%B3%E3%81%AE%E4%B8%80%E8%A6%A7)
  - [胴](#%E8%83%B4)
  - [頭](#%E9%A0%AD)
  - [脚](#%E8%84%9A)
  - [腕](#%E8%85%95)
  - [指](#%E6%8C%87)
- [ヒューマノイドボーンの親子関係](#%E3%83%92%E3%83%A5%E3%83%BC%E3%83%9E%E3%83%8E%E3%82%A4%E3%83%89%E3%83%9C%E3%83%BC%E3%83%B3%E3%81%AE%E8%A6%AA%E5%AD%90%E9%96%A2%E4%BF%82)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## ヒューマノイドボーンの定義と制約
ヒューマノイドボーンはある特定のノードと対応して存在します。

### 重複存在の禁止
ヒューマノイドボーンは vrm 内で同じものが複数存在してはいけません。

### 対応するノードに対する制約
ヒューマノイドボーンに対応するノードのスケールの各要素は 0 より大きな正の値である必要があります。

### 必須条件の定義
ヒューマノイドボーンは必須か否かの定義を持ちます。

* 必須であるヒューマノイドボーンはかならず vrm 内に存在する必要があります。
* 必須でないヒューマノイドボーンは存在しなくても許容します。

### 親ボーンの定義とその制約
ヒューマノイドボーンは親ボーンの定義を持ちます。

* 定義された親ボーンの子孫ノードである必要があります。
  * 親ボーンとの間にヒューマノイドボーンではないノードが存在することを許容します。
* 定義された親ボーンが vrm 内に存在しない場合、再帰的に親ボーンの親ボーンを辿って親ボーンとみなす必要があります。
  * 親ボーンが必須なヒューマノイドボーンではない場合、定義された親ボーンが存在しないことがあります。

#### 例
* spine の親ボーンは hips であり、hips の子孫ノードである必要があります。
* head の親ボーンは、neck, upperChest, chest が存在しないとき、spine であり、spine の子孫ノードである必要があります。

### 依存ボーンの定義とその制約
ヒューマノイドボーンは依存ボーンの定義を持ちます。

あるヒューマノイドボーンの存在は、依存ボーンに定義された特定のヒューマノイドボーンの存在に依存します。

#### 例
* spine の存在には、hips の存在が必要です。
* upperChest の存在には、chest の存在が必要です。
* leftIndexDistal の存在には、leftIndexIntermediate の存在が必要です。

## ヒューマノイドボーンの一覧

### 胴

|    名前    | 必須 |  親ボーン  | 依存ボーン | 位置の目安 |                           備考                           |
| ---------- | ---- | ---------- | ---------- | ---------- | -------------------------------------------------------- |
| hips       | Yes  | (root)     | (root)     | 股間       | 通常、このボーンだけが移動し、他のボーンは回転だけします |
| spine      | Yes  | hips       | hips       | 骨盤の上端 |                                                          |
| chest      | No   | spine      | spine      | 胸郭の下端 | 0.X では必須でした                                       |
| upperChest | No   | chest      | **chest**  |            | chest ボーンの存在に依存します                           |
| neck       | No   | upperChest | spine      | 首の付け根 | 0.X では必須でした                                       |

### 頭

|   名前   | 必須 | 親ボーン | 依存ボーン | 位置の目安 |                                                                    備考                                                                     |
| -------- | ---- | -------- | ---------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| head     | Yes  | neck     | spine      | 首の上端   |                                                                                                                                             |
| leftEye  | No   | head     | head       |            | [`VRMC_vrm.lookAt` 視線制御(オプション)](#vrmc_vrmlookat-%E8%A6%96%E7%B7%9A%E5%88%B6%E5%BE%A1%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3) |
| rightEye | No   | head     | head       |            | [`VRMC_vrm.lookAt` 視線制御(オプション)](#vrmc_vrmlookat-%E8%A6%96%E7%B7%9A%E5%88%B6%E5%BE%A1%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3) |
| jaw      | No   | head     | head       |            |                                                                                                                                             |

### 脚

|     名前      | 必須 |   親ボーン    |  依存ボーン   |   位置の目安   | 備考 |
| ------------- | ---- | ------------- | ------------- | -------------- | ---- |
| leftUpperLeg  | Yes  | hips          | hips          | 脚の付け根     |      |
| leftLowerLeg  | Yes  | leftUpperLeg  | leftUpperLeg  | 膝             |      |
| leftFoot      | Yes  | leftLowerLeg  | leftLowerLeg  | 足首           |      |
| leftToes      | No   | leftFoot      | leftFoot      | 足の指の付け根 |      |
| rightUpperLeg | Yes  | hips          | hips          | 脚の付け根     |      |
| rightLowerLeg | Yes  | rightUpperLeg | rightUpperLeg | 膝             |      |
| rightFoot     | Yes  | rightLowerLeg | rightLowerLeg | 足首           |      |
| rightToes     | No   | rightFoot     | rightFoot     | 足の指の付け根 |      |

### 腕

|     名前      | 必須 |   親ボーン    |  依存ボーン   |  位置の目安  | 備考 |
| ------------- | ---- | ------------- | ------------- | ------------ | ---- |
| leftShoulder  | No   | upperChest    | spine         |              |      |
| leftUpperArm  | Yes  | leftShoulder  | spine         | 上腕の付け根 |      |
| leftLowerArm  | Yes  | leftUpperArm  | leftUpperArm  | 肘           |      |
| leftHand      | Yes  | leftLowerArm  | leftLowerArm  | 手首         |      |
| rightShoulder | No   | upperChest    | spine         |              |      |
| rightUpperArm | Yes  | rightShoulder | spine         | 上腕の付け根 |      |
| rightLowerArm | Yes  | rightUpperArm | rightUpperArm | 肘           |      |
| rightHand     | Yes  | rightLowerArm | rightLowerArm | 手首         |      |

### 指

|          名前           | 必須 |        親ボーン         |         依存ボーン          | 位置の目安 |                       備考                       |
| ----------------------- | ---- | ----------------------- | --------------------------- | ---------- | ------------------------------------------------ |
| leftThumbProximal       | No   | leftHand                | leftHand                    |            |                                                  |
| leftThumbIntermediate   | No   | leftThumbProximal       | **leftThumbProximal**       |            | leftThumbProximal ボーンの存在に依存します       |
| leftThumbDistal         | No   | leftThumbIntermediate   | **leftThumbIntermediate**   |            | leftThumbIntermediate ボーンの存在に依存します   |
| leftIndexProximal       | No   | leftHand                | leftHand                    |            |                                                  |
| leftIndexIntermediate   | No   | leftIndexProximal       | **leftIndexProximal**       |            | leftIndexProximal ボーンの存在に依存します       |
| leftIndexDistal         | No   | leftIndexIntermediate   | **leftIndexIntermediate**   |            | leftIndexIntermediate ボーンの存在に依存します   |
| leftMiddleProximal      | No   | leftHand                | leftHand                    |            |                                                  |
| leftMiddleIntermediate  | No   | leftMiddleProximal      | **leftMiddleProximal**      |            | leftMiddleProximal ボーンの存在に依存します      |
| leftMiddleDistal        | No   | leftMiddleIntermediate  | **leftMiddleIntermediate**  |            | leftMiddleIntermediate ボーンの存在に依存します  |
| leftRingProximal        | No   | leftHand                | leftHand                    |            |                                                  |
| leftRingIntermediate    | No   | leftRingProximal        | **leftRingProximal**        |            | leftRingProximal ボーンの存在に依存します        |
| leftRingDistal          | No   | leftRingIntermediate    | **leftRingIntermediate**    |            | leftRingIntermediate ボーンの存在に依存します    |
| leftLittleProximal      | No   | leftHand                | leftHand                    |            |                                                  |
| leftLittleIntermediate  | No   | leftLittleProximal      | **leftLittleProximal**      |            | leftLittleProximal ボーンの存在に依存します      |
| leftLittleDistal        | No   | leftLittleIntermediate  | **leftLittleIntermediate**  |            | leftLittleIntermediate ボーンの存在に依存します  |
| rightThumbProximal      | No   | rightHand               | rightHand                   |            |                                                  |
| rightThumbIntermediate  | No   | rightThumbProximal      | **rightThumbProximal**      |            | rightThumbProximal ボーンの存在に依存します      |
| rightThumbDistal        | No   | rightThumbIntermediate  | **rightThumbIntermediate**  |            | rightThumbIntermediate ボーンの存在に依存します  |
| rightIndexProximal      | No   | rightHand               | rightHand                   |            |                                                  |
| rightIndexIntermediate  | No   | rightIndexProximal      | **rightIndexProximal**      |            | rightIndexProximal ボーンの存在に依存します      |
| rightIndexDistal        | No   | rightIndexIntermediate  | **rightIndexIntermediate**  |            | rightIndexIntermediate ボーンの存在に依存します  |
| rightMiddleProximal     | No   | rightHand               | rightHand                   |            |                                                  |
| rightMiddleIntermediate | No   | rightMiddleProximal     | **rightMiddleProximal**     |            | rightMiddleProximal ボーンの存在に依存します     |
| rightMiddleDistal       | No   | rightMiddleIntermediate | **rightMiddleIntermediate** |            | rightMiddleIntermediate ボーンの存在に依存します |
| rightRingProximal       | No   | rightHand               | rightHand                   |            |                                                  |
| rightRingIntermediate   | No   | rightRingProximal       | **rightRingProximal**       |            | rightRingProximal ボーンの存在に依存します       |
| rightRingDistal         | No   | rightRingIntermediate   | **rightRingIntermediate**   |            | rightRingIntermediate ボーンの存在に依存します   |
| rightLittleProximal     | No   | rightHand               | rightHand                   |            |                                                  |
| rightLittleIntermediate | No   | rightLittleProximal     | **rightLittleProximal**     |            | rightLittleProximal ボーンの存在に依存します     |
| rightLittleDistal       | No   | rightLittleIntermediate | **rightLittleIntermediate** |            | rightLittleIntermediate ボーンの存在に依存します |

## ヒューマノイドボーンの親子関係の図示

ヒューマノイドボーンの親子関係と必須条件を以下に図示します。

* root(ヒューマノイドボーンではない。原点)
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
                  * (fingers) 省略
          * (rightShoulder)
            * rightUpperArm
              * rightLowerArm
                * rightHand
                  * (fingers) 省略
    * leftUpperLeg
      * leftLowerLeg
        * leftFoot
          * (leftToes)
    * rightUpperLeg
      * rightLowerLeg
        * rightFoot
          * (leftToes)
