# `VRMC_vrm.lookAt`

本文書では、 `VRMC_vrm` 拡張のうち `lookAt` フィールドについての仕様を示します。

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [概要](#%E6%A6%82%E8%A6%81)
- [LookAt 空間(offsetFromHeadBone)](#lookat-%E7%A9%BA%E9%96%93offsetfromheadbone)
- [項目](#%E9%A0%85%E7%9B%AE)
  - [LookAtType](#lookattype)
  - [角度調整項](#%E8%A7%92%E5%BA%A6%E8%AA%BF%E6%95%B4%E9%A0%85)
- [LookAtのアルゴリズム](#lookat%E3%81%AE%E3%82%A2%E3%83%AB%E3%82%B4%E3%83%AA%E3%82%BA%E3%83%A0)
  - [Yaw and Pitch in lookAt space](#yaw-and-pitch-in-lookat-space)
  - [Apply Yaw and Pitch to bone](#apply-yaw-and-pitch-to-bone)
  - [Apply Yaw and Pitch to expression](#apply-yaw-and-pitch-to-expression)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## 概要

LookAtは、VRMモデルに対して視線のアニメーションを行うためのコンポーネントです。

初期姿勢時の `Head` ボーンをオフセットして得られる `LookAt空間` で注視点を評価して、視線方向を決定します。
視線値は、`LookAt空間` での Yaw, Pitch の Degree 値であるとします。
(Yaw, Pitch は、上記のように注視点から求めても任意の方法で生成してもよい）。

`LookAt` は `Bone` タイプ、`Expression` タイプが定義されており、
Expressionタイプにはさらに `morph target` と `UV offset scale` があります。

一組の yaw, pitch を想定していて両目が同じ方向を見ることを想定しています。
このことより、寄り目等の表現は想定されていません。

## LookAt 空間(offsetFromHeadBone)

視線方向の基準となる空間で、ヘッドボーンのローカル座標の `offsetFromHeadBone` を原点に持ち、初期姿勢時のヘッドボーンのワールド逆回転を持ちます。
(root が回転無しの場合、ワールド軸と同じ向き。）

右手系なので Yaw, Pitch の正の方向は下記のとおりです。
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

## 項目

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

| 名前       | 備考                                                                |
|:-----------|:--------------------------------------------------------------------|
| bone       | Humanoid で規定された leftEyeボーンとrightEyeボーンで視線制御します |
| expression | Expression のLookAt, LookDown, LookLeft, LookRightで視線制御します  |

expression は、

MorphTarget タイプと TextureUVOffset タイプが可能です。
どちらも Expression として処理します。

### 角度調整項

#### type が bone のときの 解釈

`leftEye` ボーンと `rightEye` ボーンに対する `local rotation` を生成します。
`水平内側`, `水平外側`, `垂直上側`, `垂直下側` の４つの区分があります。

水平内側と外側の使い分けは下記のようになっています。

```
     ^           ^
     |           |
outer|inner inner|outer
   left        right
```

|             | leftEye rangeMap        | rightEye rangeMap       |
|-------------|-------------------------|-------------------------|
| Yaw>0(左)   | rangeMapHorizontalOuter | rangeMapHorizontalInner |
| Yaw<0(右)   | rangeMapHorizontalInner | rangeMapHorizontalOuter |
| Pitch>0(下) | rangeMapVerticalDown    | rangeMapVerticalDown    |
| Pitch<0(上) | rangeMapVerticalUp      | rangeMapVerticalUp      |

角度調整処理は絶対値に対して行います。

```
const boneLocalEulerAngle = min(fabs(value), inputMaxValue)/inputMaxValue * outputScale;
```

#### type が expression のときの解釈

`lookUp` Expression, `lookDown` Expression, `lookLeft` Expression, `lookRight` Expression に対する `weight` を生成します。
`水平`, `垂直上側`, `垂直下側` の3つの区分があります。
ひとつの Expression で両目がまとめて変化するため、`水平内側`, `水平外側` の区別が無いことに注意してください。
`水平` として `rangeMapHorizontalOuter` を使います。

|             | expression | rangeMap                |
|-------------|------------|-------------------------|
| Yaw>0(左)   | lookLeft   | rangeMapHorizontalOuter |
| Yaw<0(右)   | lookRight  | rangeMapHorizontalOuter |
| Pitch>0(下) | lookDown   | rangeMapVerticalDown    |
| Pitch<0(上) | lookUp     | rangeMapVerticalUp      |

角度調整処理は絶対値に対して行います。

```
const expressionWeight = min(fabs(value), inputMaxValue)/inputMaxValue * outputScale;
```

## LookAtのアルゴリズム
### Yaw and Pitch in lookAt space

```
// TODO: current UniVRM implementation
public static (float Yaw, float Pitch) CalcYawPitch(this Matrix4x4 m, Vector3 target)
{
    var localPosition = m.inverse.MultiplyPoint(target);

    var zaxis = Vector3.Project(localPosition, Vector3.forward);
    var yaxis = Vector3.Project(localPosition, Vector3.up);
    var xaxis = Vector3.Project(localPosition, Vector3.right);

    var xDot = Vector3.Dot(xaxis, Vector3.right) > 0;
    var yDot = Vector3.Dot(yaxis, Vector3.up) > 0;
    var zDot = Vector3.Dot(zaxis, Vector3.forward) > 0;

    // x z plane
    var yaw = (float)Math.Atan2(xaxis.magnitude, zaxis.magnitude) * Mathf.Rad2Deg;
    if (xDot && zDot)
    {
        // 1st(0-90)

    }
    else if (xDot && !zDot)
    {
        // 2nd(90-180)
        yaw = 180 - yaw;
    }
    else if (!xDot && !zDot)
    {
        // 3rd
        yaw = -180 + yaw;
    }
    else if (!xDot && zDot)
    {
        // 4th
        yaw = -yaw;
    }
    else
    {
        throw new NotImplementedException();
    }

    // x+y z plane
    var pitch = (float)Math.Atan2(yaxis.magnitude, (xaxis + zaxis).magnitude) * Mathf.Rad2Deg;
    if (yDot)
    {
        pitch = -pitch;
    }

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

function applyRightEyeBone(yawDegrees, pitchDegrees)
{
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
