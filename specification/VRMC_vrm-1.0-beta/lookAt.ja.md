# `VRMC_vrm.lookAt`

本文書では、 `VRMC_vrm` 拡張のうち `lookAt` フィールドについての仕様を示します。

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [概要](#%E6%A6%82%E8%A6%81)
- [詳細](#%E8%A9%B3%E7%B4%B0)
  - [LookAtType](#lookattype)
  - [LookAt 空間(offsetFromHeadBone)](#lookat-%E7%A9%BA%E9%96%93offsetfromheadbone)
  - [範囲マップ](#%E7%AF%84%E5%9B%B2%E3%83%9E%E3%83%83%E3%83%97)
- [LookAtのアルゴリズム](#lookat%E3%81%AE%E3%82%A2%E3%83%AB%E3%82%B4%E3%83%AA%E3%82%BA%E3%83%A0)
  - [Yaw and Pitch in lookAt space](#yaw-and-pitch-in-lookat-space)
  - [Apply Yaw and Pitch to bone](#apply-yaw-and-pitch-to-bone)
  - [Apply Yaw and Pitch to expression](#apply-yaw-and-pitch-to-expression)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## 概要

LookAtは、VRMモデルに対して視線のアニメーションを行うためのコンポーネントです。

初期姿勢時の `Head` ボーンをオフセットして得られる `LookAt空間` で視線を定義します。
視線値は、`LookAt空間` での 上下左右 の Degree 値です。
本文章では、右手系の Euler 角に準じた正の回転方向をもつ Yaw と Pitch で説明します。

> プラットフォーム毎に、右手・左手、 Y-UP・Z-UP などをふまえて一貫性のある実装をしてください。

一組の yaw, pitch を想定していて両目が同じ方向を見ることを想定しています。
このことより、寄り目等の表現は想定されていません。

## 詳細

```json
extensions.VRMC_vrm.lookAt = {
  "offsetFromHeadBone": [
    0,
    0.06,
    0
  ],
  "rangeMapHorizontalInner": {
    "inputMaxValue": 90,
    "outputScale": 10
  },
  "rangeMapHorizontalOuter": {
    "inputMaxValue": 90,
    "outputScale": 10
  },
  "rangeMapVerticalDown": {
    "inputMaxValue": 90,
    "outputScale": 10
  },
  "rangeMapVerticalUp": {
    "inputMaxValue": 90,
    "outputScale": 10
  },
  "type": "bone"
}
```

| 名前                    | 備考                                                                 |
|:------------------------|:---------------------------------------------------------------------|
| type                    | bone または expression                                               |
| offsetFromHeadBone      | lookAtの基準位置(両目の間が目安)へのヘッドボーンからの位置offsetです |
| rangeMapHorizontalInner | 水平内側の目の可動範囲                                               |
| rangeMapHorizontalOuter | 水平外側の目の可動範囲(ExpressionのLookLeft, LookRightはこれを使用)  |
| rangeMapVerticalDown    | 下方向の目の可動範囲                                                 |
| rangeMapVerticalUp      | 上方向の目の可動範囲                                                 |

### LookAtType

下記の2種類を定義しています。

| 名前       | 対象                                                    | 値               |
|:-----------|:--------------------------------------------------------|------------------|
| bone       | Humanoid leftEyeボーンとrightEyeボーン の LocalRotation | EulerAngles      |
| expression | Expression のLookAt, LookDown, LookLeft, LookRight      | ExpressionWeight |

> expression は、MorphTarget, MaterialColor, TextureTransform が可能です。
> LookAt では、おもに MorphTarget による頂点移動 と TextureTransform による 目のテクスチャーの offset 移動により視線が表現されることが想定されています。

### LookAt 空間(offsetFromHeadBone)

視線方向の基準となる空間で、`head` ボーンのローカル座標の `offsetFromHeadBone` を原点に持ち、初期姿勢時のヘッドボーンのワールド逆回転を持ちます。
root が回転無しの場合、ワールド軸と同じ向きです。

本文章では、glTF の右手系, Y-Up, Z-Forward 座標系を用いて説明します。
Yaw, Pitch の正の方向は下記のとおりです。

* Yaw: Z->X方向 => 左
* Pitch: Y->Z方向 => 下

```
      Y  Forward
      ^  Z
      | /
      |/
X<----+
Left      Right
```

`offsetFromHeadBone` は、VR向けHMDの位置を想定しています。
モデルの一人称視点の位置の取得・反映に用いることができます。

> Implementation note: モデルに `offsetFromHeadBone` が存在しない場合は、実装ごとに適切な値にフォールバックを行うことが推奨されます。


### 範囲マップ

`LookAt空間` で評価された視線値 `Yaw` と `Pitch` を `bone` または `expression` に適用する前に、値を加工できます。

| name          | 機能                                                                                    |
|---------------|-----------------------------------------------------------------------------------------|
| inputMaxValue | Yaw または Pitch の上限値。この値が小さいほど、同じ視線値に対して視線は大きく動きます。 |
| outputScale   | `bone の回転` または `Expression の Weight` の最大値。                                  |

#### type が bone のときの 解釈

`yaw`, `pitch` の視線値から、`leftEye` ボーンと `rightEye` ボーンに対する `local rotation` を生成します。
`水平内側`, `水平外側`, `垂直上側`, `垂直下側` の４つの区分があります。

上下左右の rangeMap の使い分けは下記のようになります。

```
  + yaw -     + yaw -
     ^           ^
     |           |
outer|inner inner|outer
  left eye    right eye
```

|             | leftEye rangeMap        | rightEye rangeMap       |
|-------------|-------------------------|-------------------------|
| Yaw>0(左)   | rangeMapHorizontalOuter | rangeMapHorizontalInner |
| Yaw<0(右)   | rangeMapHorizontalInner | rangeMapHorizontalOuter |
| Pitch>0(下) | rangeMapVerticalDown    | rangeMapVerticalDown    |
| Pitch<0(上) | rangeMapVerticalUp      | rangeMapVerticalUp      |

範囲マップは視線値の絶対値に対して行います。

```
const boneLocalEulerAngle = min(fabs(value), inputMaxValue)/inputMaxValue * outputScale;
```

#### type が expression のときの解釈

`lookUp` Expression, `lookDown` Expression, `lookLeft` Expression, `lookRight` Expression に対する `weight` を生成します。
`水平`, `垂直上側`, `垂直下側` の3つの区分があります。
ひとつの Expression で両目がまとめて変化するため、`水平内側`, `水平外側` の区別が無いことに注意してください。
`rangeMapHorizontalOuter` を使います。

|             | expression | rangeMap                |
|-------------|------------|-------------------------|
| Yaw>0(左)   | lookLeft   | rangeMapHorizontalOuter |
| Yaw<0(右)   | lookRight  | rangeMapHorizontalOuter |
| Pitch>0(下) | lookDown   | rangeMapVerticalDown    |
| Pitch<0(上) | lookUp     | rangeMapVerticalUp      |

範囲マップは絶対値に対して行います。

```
const expressionWeight = min(fabs(value), inputMaxValue)/inputMaxValue * outputScale;
```

## LookAtのアルゴリズム
### Yaw and Pitch in lookAt space

```cs
public static (float Yaw, float Pitch) CalcYawPitch(this Matrix4x4 lookAtSpace, Vector3 target)
{
    var localTarget = lookAtSpace.inverse.MultiplyPoint(target);

    var z = Vector3.Dot(localPosition, Vector3.forward);
    var x = Vector3.Dot(localPosition, Vector3.right);
    var yaw = (float)Math.Atan2(x, z) * Mathf.Rad2Deg;

    // x+y z plane
    var xz = Mathf.sqrt(x * x + z * z);
    var y = Vector3.Dot(localPosition, Vector3.up);
    var pitch = (float)Math.Atan2(-y, xz) * Mathf.Rad2Deg;

    return (yaw, pitch);
}
```

### Apply Yaw and Pitch to bone

```
function applyLeftEyeBone(vrm, yawDegrees, pitchDegrees)
{    
  var yaw = 0;
  if(yawDegrees>0)
  {
    // left => outer
    yaw = min(fabs(yawDegrees), rangeMapHorizontalOuter.inputMaxValue) / rangeMapHorizontalOuter.inputMaxValue * rangeMapHorizontalOuter.outputScale;
  }
  else{
    // right => inner
    yaw = -min(fabs(yawDegrees), rangeMapHorizontalInner.inputMaxValue) / rangeMapHorizontalInner.inputMaxValue * rangeMapHorizontalInner.outputScale;
  }

  var pitch = 0;
  if(pitchDegrees>0)
  {
    // down
    pitch = min(fabs(pitchDegrees), rangeMapVerticalDown.inputMaxValue) / rangeMapVerticalDown.inputMaxValue * rangeMapVerticalDown.outScale;
  }
  else{
    // up
    pitch = -min(fabs(pitchDegrees), rangeMapVerticalUp.inputMaxValue) / rangeMapVerticalUp.inputmaxValue * rangeMapVerticalUp.outScale;
  }

  vrm.humanoid.leftEye.localRotation = Quaternion.from_YXZEuler(yaw, pitch, 0);
}

function applyLeftEyeBone(vrm, yawDegrees, pitchDegrees)
{    
  var yaw = 0;
  if(yawDegrees>0)
  {
    // left => inner
    yaw = min(fabs(yawDegrees), rangeMapHorizontalInner.inputMaxValue) / rangeMapHorizontalInner.inputMaxValue * rangeMapHorizontalInner.outputScale;
  }
  else{
    // right => outer
    yaw = -min(fabs(yawDegrees), rangeMapHorizontalOuter.inputMaxValue) / rangeMapHorizontalOuter.inputMaxValue * rangeMapHorizontalOuter.outputScale;
  }

  var pitch = 0;
  if(pitchDegrees>0)
  {
    // down
    pitch = min(fabs(pitchDegrees), rangeMapVerticalDown.inputMaxValue) / rangeMapVerticalDown.inputMaxValue * rangeMapVerticalDown.outScale;
  }
  else{
    // up
    pitch = -min(fabs(pitchDegrees), rangeMapVerticalUp.inputMaxValue) / rangeMapVerticalUp.inputmaxValue * rangeMapVerticalUp.outScale;
  }

  vrm.humanoid.rightEye.localRotation = Quaternion.from_YXZEuler(yaw, pitch, 0);
}
```

### Apply Yaw and Pitch to expression

```
function applyExpression(vrm, yawDegrees, pitchDegrees)
{
  // horizontal
  if(yawWeight>0)
  {
    // left
    const yawWeight = min(fabs(yawDegrees), rangeMapHorizontalOuter.inputMaxValue)/rangeMapHorizontalOuter.inputMaxValue * rangeMapHorizontalOuter.outputScale;
    vrm.expression.setWeight(LOOK_LEFT, yawWeight);
    vrm.expression.setWeight(LOOK_RIGHT, 0);
  }
  else{
    // right
    const yawWeight = min(fabs(yawDegrees), rangeMapHorizontalOuter.inputMaxValue)/rangeMapHorizontalOuter.inputMaxValue * rangeMapHorizontalOuter.outputScale;
    vrm.expression.setWeight(LOOK_LEFT, 0);
    vrm.expression.setWeight(LOOK_RIGHT, yawWeight);
  }

  // vertical
  if(pitchWeight>0)
  {
    // down
    const pitchWeight = min(fabs(yawDegrees), rangeMapVerticalDown.inputMaxValue)/rangeMapVerticalDown.inputMaxValue * rangeMapVerticalDown.outputScale;
    vrm.expression.setWeight(LOOK_DOWN, pitchWeight);
    vrm.expression.setWeight(LOOK_UP, 0);
  }
  else{
    // up
    const pitchWeight = min(fabs(yawDegrees), rangeMapVerticalUp.inputMaxValue)/rangeMapVerticalUp.inputMaxValue * rangeMapVerticalUp.outputScale;
    vrm.expression.setWeight(LOOK_DOWN, 0);
    vrm.expression.setWeight(LOOK_UP, pitchWeight);
  }
}
```
