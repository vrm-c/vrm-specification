# `VRMC_vrm.humanoid`

本文書では、 `VRMC_vrm` 拡張のうち `humanoid` フィールドについての仕様を示します。
`humanoid bone` の一覧と `T-Pose` を定義します。

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
- [ヒューマノイドの T-Pose](#%E3%83%92%E3%83%A5%E3%83%BC%E3%83%9E%E3%83%8E%E3%82%A4%E3%83%89%E3%81%AE-t-pose)
  - [各ボーンの向き](#%E5%90%84%E3%83%9C%E3%83%BC%E3%83%B3%E3%81%AE%E5%90%91%E3%81%8D)
- [ノードから回転・スケールを除去する手順](#%E3%83%8E%E3%83%BC%E3%83%89%E3%81%8B%E3%82%89%E5%9B%9E%E8%BB%A2%E3%83%BB%E3%82%B9%E3%82%B1%E3%83%BC%E3%83%AB%E3%82%92%E9%99%A4%E5%8E%BB%E3%81%99%E3%82%8B%E6%89%8B%E9%A0%86)
  - [回転・スケールを除去しない場合](#%E5%9B%9E%E8%BB%A2%E3%83%BB%E3%82%B9%E3%82%B1%E3%83%BC%E3%83%AB%E3%82%92%E9%99%A4%E5%8E%BB%E3%81%97%E3%81%AA%E3%81%84%E5%A0%B4%E5%90%88)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## ヒューマノイドボーンの一覧

* ヒューマノイドボーンは vrm 内で同じものが複数存在してはいけません。

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

## ヒューマノイドボーンの親子関係

* ヒューマノイドボーンは親ボーンが決まっています。必須でない親ボーンが存在しない場合は、親ボーンの親ボーンを探してください。
* ヒューマノイドボーンの間にヒューマノイドボーンではないノードが入ることは許容されます(UpperLegとLowerLegの間にヒューマノイドボーンでないノードがあるなど)

hips を root として以下のような親子になります。

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

## ヒューマノイドの制約

* Root(hipsの親)は原点
* Y = 0 で接地している
* Z+ の方向を向いている( 0.X では Z- 向き)
* 各ノードのミラー・スケールがクリアされている
  * vrm0と違い回転は残ります

## ヒューマノイドの T-Pose

T-Pose からの相対回転でポーズを表すことを想定します。
`vrm0` ではこのため初期姿勢を T-Pose としていました。

`vrm1` ではヒューマノイドボーン座標から T-Pose を作れるようにします。

## ヒューマノイドボーン座標

* humanoid bone と tail を結ぶ方向ベクトル rollDir(tail の決め方は後述します)
* ロール軸と直交するボーンの方向性を確定させる追加の axisDir
  * axisDir のデフォルト値は [0, 0, 1] とします。例外として leftToes と rightToes のみ [0, 1, 0] とします。

からヒューマノイドボーンの向きを算出します。
下記のように正規直交座標を得ます。

```
var Y = rollAxis.normalized;
var Z = axisDir.normalized; // rollAxis と完全に直交していなくてもよい

var X = Vector3.cross(Z, Y);
Z = Vector3.cross(X, Y);

var R = Matrix(X, Y, Z);
```

### tail の決め方

* 子孫に humanoid bone が見つかる
  * hips の場合は spine(leftUpperLeg, RightUpperLeg を tail に選ばない)
  * chest の場合は neck(leftUpperArm, RightUpperArm を tail に選ばない)
* 末端の humanoid bone は children[0]。無ければ親ボーンからまっすぐに延長したものを位置 tail があるする

### 胴

| ボーン名前 | axisDir | 備考 |
|:-----------|:--------|------|
| hips       | forward |      |
| spine      | forward |      |
| chest      | forward |      |
| upperChest | forward |      |
| neck       | forward |      |

### 頭

| ボーン名前 | axisDir  | 備考 |
|:-----------|:---------|:-----|
| head       | forward  |      |
| leftEye    | 視線方向 |      |
| rightEye   | 視線方向 |      |
| jaw        | forward  |      |

### 脚

直立状態です。

| ボーン名前    | axisDir          | 備考 |
|:--------------|:-----------------|:-----|
| leftUpperLeg  | forward          |      |
| leftLowerLeg  | forward          |      |
| leftFoot      | 足の甲の斜め向き |      |
| leftToes      | up               |      |
| rightUpperLeg | forward          |      |
| rightLowerLeg | forward          |      |
| rightFoot     | 足の甲の斜め向き |      |
| rightToes     | up               |      |

### 腕

腕(upperArm, lowerArm, handを結ぶ線)を水平にし、掌を下に向けます。

| ボーン名前    | axisDir  | 備考 |
|:--------------|:---------|:-----|
| leftShoulder  | forward  |      |
| leftUpperArm  | forward  |      |
| leftLowerArm  | 肘の内側 |      |
| leftHand      | 親指の方 |      |
| rightShoulder | forward  |      |
| rightUpperArm | forward  |      |
| rightLowerArm | 肘の内側 |      |
| rightHand     | 親指の方 |      |

### 指

| ボーン名前              | axisDir  | 備考 |
|:------------------------|:---------|:-----|
| leftThumbProximal       | 指の外側 |      |
| leftThumbIntermediate   | 指の外側 |      |
| leftThumbDistal         | 指の外側 |      |
| leftIndexProximal       | 親指の方 |      |
| leftIndexIntermediate   | 親指の方 |      |
| leftIndexDistal         | 親指の方 |      |
| leftMiddleProximal      | 親指の方 |      |
| leftMiddleIntermediate  | 親指の方 |      |
| leftMiddleDistal        | 親指の方 |      |
| leftRingProximal        | 親指の方 |      |
| leftRingIntermediate    | 親指の方 |      |
| leftRingDistal          | 親指の方 |      |
| leftLittleProximal      | 親指の方 |      |
| leftLittleIntermediate  | 親指の方 |      |
| leftLittleDistal        | 親指の方 |      |
| rightThumbProximal      | 指の外側 |      |
| rightThumbIntermediate  | 指の外側 |      |
| rightThumbDistal        | 指の外側 |      |
| rightIndexProximal      | 親指の方 |      |
| rightIndexIntermediate  | 親指の方 |      |
| rightIndexDistal        | 親指の方 |      |
| rightMiddleProximal     | 親指の方 |      |
| rightMiddleIntermediate | 親指の方 |      |
| rightMiddleDistal       | 親指の方 |      |
| rightRingProximal       | 親指の方 |      |
| rightRingIntermediate   | 親指の方 |      |
| rightRingDistal         | 親指の方 |      |
| rightLittleProximal     | 親指の方 |      |
| rightLittleIntermediate | 親指の方 |      |
| rightLittleDistal       | 親指の方 |      |
