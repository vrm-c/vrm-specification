# `VRMC_vrm.humanoid`

本文書では、 `VRMC_vrm` 拡張のうち `humanoid` フィールドについての仕様を示します。
`glTF の シーン Node` と `humanoid bone` の対応関係を表します。
また、 `T-Pose` について記述します。

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

## ヒューマノイドボーンのルール

* ヒューマノイドボーンはユニークで同じものが複数存在することはありません。
* ヒューマノイドボーンは決まった親子関係を持ちます。
* ヒューマノイドボーンの間にヒューマノイドボーンではないノードが入ることは許容します(UpperLegとLowerLegの間にヒューマノイドボーンでないノードがあるなど)

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

## ヒューマノイドの T-Pose

VRM では、初期姿勢 T-Pose が必要です。
T-Pose は、以下の項目と後述する各ボーンの曲げ軸の方向性に従ってください。

* Root(hipsの親)は原点
* Y = 0 で接地している
* Z+ の方向を向いている( 0.X では Z- 向き)
* 各ノードの回転・スケールがクリアされている

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

### T-Pose bake の手順

T-Pose の制約

`ノードの回転・スケールをクリアする`

のためには、`Mesh`, `Skin`, `Node` の再構築が必要です。

シーンで準備

* メートルスケールにする
* T-Poseにする

処理

* mesh(skinなし)
  * 各頂点にNode.worldMatrixを乗算する
* mesh(skinあり)
  * BoneWeightの無い頂点がもしあれば、skin.root に対するBoneWeightを付与する
  * メッシュをスキニングして結果をメッシュとして再取り込みする
* Nodeの回転・スケールを除去する

により

`skins[*].inverseBindMatrices` は、

```js
[1, 0, 0, 0]
[0, 1, 0, 0]
[0, 0, 1, 0]
[-x, -y, -z, 1] // x, y, z = joint のワールド座標
```

と単純化されます。

ベースになったモデルの `inverseBindMatrices` に
回転(Z-UPの吸収)やスケール(負の値を含む)が含まれている場合があり、
これはアプリケーションで

* ボーン位置とメッシュ位置の不一致
* 面の表裏の誤判定

などの予期しない結果を誘発します。
