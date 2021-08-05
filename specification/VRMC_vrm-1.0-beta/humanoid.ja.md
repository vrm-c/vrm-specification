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

## ヒューマノイドの T-Pose

VRM では、初期姿勢 T-Pose が必要です。
T-Pose は、以下の項目と後述する各ボーンの曲げ軸の方向性に従ってください。

* Root(hipsの親)は原点
* Y = 0 で接地している
* Z+ の方向を向いている( 0.X では Z- 向き)
* 各ノードの回転・スケールがクリアされている
* 足、胴体、頭が直立状態
* 腕が水平
* 掌は下向き

### 各ボーンの向き

各ボーンの曲げ軸が指定されたワールド軸になるようにしてください。

#### 胴

直立状態です。

| ボーン名前 | 曲げ軸 | 備考 |
|:-----------|:-------|------|
| hips       |        |      |
| spine      |        |      |
| chest      |        |      |
| upperChest |        |      |
| neck       |        |      |

#### 頭

正面向きです。

| ボーン名前 | 曲げ軸 | 備考                              |
|:-----------|:-------|:----------------------------------|
| head       |        |                                   |
| leftEye    |        | 視線がZ軸になるようにしてください |
| rightEye   |        | 視線がZ軸になるようにしてください |
| jaw        |        |                                   |

#### 脚

直立状態です。

| ボーン名前    | 曲げ軸 | 備考 |
|:--------------|:-------|:-----|
| leftUpperLeg  |        |      |
| leftLowerLeg  | X軸    |      |
| leftFoot      | X軸    |      |
| leftToes      | X軸    |      |
| rightUpperLeg |        |      |
| rightLowerLeg | X軸    |      |
| rightFoot     | X軸    |      |
| rightToes     | X軸    |      |

#### 腕

腕(upperArm, lowerArm, handを結ぶ線)を水平にし、掌を下に向けます。

| ボーン名前    | 曲げ軸 | 備考               |
|:--------------|:-------|:-------------------|
| leftShoulder  |        |                    |
| leftUpperArm  |        |                    |
| leftLowerArm  | Y軸    |                    |
| leftHand      |        | 掌が下になるように |
| rightShoulder |        |                    |
| rightUpperArm |        |                    |
| rightLowerArm | Y軸    |                    |
| rightHand     |        | 掌が下になるように |

#### 指

* X軸に平行にします。
* 親指を斜めにする場合、XY平面で回転してください。
  * この場合、曲げ軸は親指と垂直になります

| ボーン名前              | 曲げ軸 | 備考                     |
|:------------------------|:-------|:-------------------------|
| leftThumbProximal       | Y軸    | 人差し指に最接近した状態 |
| leftThumbIntermediate   | Y軸    | 人差し指に最接近した状態 |
| leftThumbDistal         | Y軸    | 人差し指に最接近した状態 |
| leftIndexProximal       | Z軸    |                          |
| leftIndexIntermediate   | Z軸    |                          |
| leftIndexDistal         | Z軸    |                          |
| leftMiddleProximal      | Z軸    |                          |
| leftMiddleIntermediate  | Z軸    |                          |
| leftMiddleDistal        | Z軸    |                          |
| leftRingProximal        | Z軸    |                          |
| leftRingIntermediate    | Z軸    |                          |
| leftRingDistal          | Z軸    |                          |
| leftLittleProximal      | Z軸    |                          |
| leftLittleIntermediate  | Z軸    |                          |
| leftLittleDistal        | Z軸    |                          |
| rightThumbProximal      | Y軸    | 人差し指に最接近した状態 |
| rightThumbIntermediate  | Y軸    | 人差し指に最接近した状態 |
| rightThumbDistal        | Y軸    | 人差し指に最接近した状態 |
| rightIndexProximal      | Z軸    |                          |
| rightIndexIntermediate  | Z軸    |                          |
| rightIndexDistal        | Z軸    |                          |
| rightMiddleProximal     | Z軸    |                          |
| rightMiddleIntermediate | Z軸    |                          |
| rightMiddleDistal       | Z軸    |                          |
| rightRingProximal       | Z軸    |                          |
| rightRingIntermediate   | Z軸    |                          |
| rightRingDistal         | Z軸    |                          |
| rightLittleProximal     | Z軸    |                          |
| rightLittleIntermediate | Z軸    |                          |
| rightLittleDistal       | Z軸    |                          |

## ノードから回転・スケールを除去する手順

シーン準備

* メートルスケールで正しい大きさになるようにする
* モデルを原点で Z+ 向きにする
* モデルを T-Pose にする

処理

* mesh(skinなし)
  * 各頂点にNode.worldMatrixを乗算してメッシュとして再取り込みする
* mesh(skinあり)
  * BoneWeightの無い頂点がもしあれば、skin.root に対するBoneWeightを付与する
  * メッシュをスキニングして結果をメッシュとして再取り込みする
* すべてのノードのワールド座標を記録
  * 親から順番に、ノードの回転・スケールを除去して、記録したワールド座標を代入する
* メッシュのバインド行列を更新する。

`skins[*].inverseBindMatrices` は、

```js
[1, 0, 0, 0]
[0, 1, 0, 0]
[0, 0, 1, 0]
[-x, -y, -z, 1] // x, y, z = joint のワールド座標
```

と単純化されます。

### 回転・スケールを除去しない場合

ベースになったモデルの `inverseBindMatrices` に
回転(Z-UPの吸収)やスケール(ミラーリングを表す負の値など)が含まれている場合があり、
これはアプリケーションで

* ボーン位置とメッシュ位置の不一致
* 面の表裏の誤判定

などの予期しない結果を起こし、モデルの制御を困難にします。
