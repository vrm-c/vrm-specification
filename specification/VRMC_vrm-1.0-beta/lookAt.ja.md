# `VRMC_vrm.lookAt`

本文書では、 `VRMC_vrm` 拡張のうち `lookAt` フィールドについての仕様を示します。

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [項目](#%E9%A0%85%E7%9B%AE)
- [LookAtType](#lookattype)
- [目の可動範囲の調整](#%E7%9B%AE%E3%81%AE%E5%8F%AF%E5%8B%95%E7%AF%84%E5%9B%B2%E3%81%AE%E8%AA%BF%E6%95%B4)
  - [水平内側](#%E6%B0%B4%E5%B9%B3%E5%86%85%E5%81%B4)
  - [水平外側](#%E6%B0%B4%E5%B9%B3%E5%A4%96%E5%81%B4)
  - [垂直下方向](#%E5%9E%82%E7%9B%B4%E4%B8%8B%E6%96%B9%E5%90%91)
  - [垂直上方向](#%E5%9E%82%E7%9B%B4%E4%B8%8A%E6%96%B9%E5%90%91)
  - [Boneタイプの可動範囲の適用](#bone%E3%82%BF%E3%82%A4%E3%83%97%E3%81%AE%E5%8F%AF%E5%8B%95%E7%AF%84%E5%9B%B2%E3%81%AE%E9%81%A9%E7%94%A8)
  - [Expressionタイプの可動範囲の適用](#expression%E3%82%BF%E3%82%A4%E3%83%97%E3%81%AE%E5%8F%AF%E5%8B%95%E7%AF%84%E5%9B%B2%E3%81%AE%E9%81%A9%E7%94%A8)
- [LookAtのアルゴリズム](#lookat%E3%81%AE%E3%82%A2%E3%83%AB%E3%82%B4%E3%83%AA%E3%82%BA%E3%83%A0)
  - [Boneタイプ](#bone%E3%82%BF%E3%82%A4%E3%83%97)
  - [Expressionタイプ](#expression%E3%82%BF%E3%82%A4%E3%83%97)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## 概要

LookAtは、VRMモデルに対して視線のアニメーションを行うためのコンポーネントです。

視線を角度 yaw, pitch ２つの角度で表しこれを目に適用します。
yaw, pitch の設定方法は、VRM実装のAPIから設定する注視点から後述する基準位置への方向ベクトルを用いて計算する手法と、直接値を入力する手法の２つが想定されます。
生成された yaw, pitch をモデルに適用する手法は、Boneタイプ、Expressionタイプが想定されます。
それぞの場合について各項目で説明します。

一組の yaw, pitch を想定していて両目が同じ方向を見ることを想定しています。
このことより、寄り目等の表現は想定されていません。

## 項目

| 名前                    | 備考                                                                 |
|:------------------------|:---------------------------------------------------------------------|
| type                    | bone または expression                                               |
| offsetFromHeadBone      | lookAtの基準位置(両目の間が目安)へのヘッドボーンからの位置offsetです |
| rangeMapHorizontalInner | 水平内側の目の可動範囲                                               |
| rangeMapHorizontalOuter | 水平外側の目の可動範囲                                               |
| rangeMapVerticalDown    | 下方向の目の可動範囲                                                 |
| rangeMapVerticalUp      | 上方向の目の可動範囲                                                 |

## LookAtType

| 名前       | 備考                                                                |
|:-----------|:--------------------------------------------------------------------|
| bone       | Humanoid で規定された leftEyeボーンとrightEyeボーンで視線制御します |
| expression | Expression のLookAt, LookDown, LookLeft, LookRightで視線制御します  |

expression は、

MorphTarget タイプと TextureUVOffset タイプが可能です。
どちらも Expression として処理します。

## LookAtの基準位置

視線値 yaw, pitch を注視点との位置関係から算出する場合に `基準位置` が利用されます。
２つの方向ベクトル `HeadBone の Forward` と `注視点 - LookAt 基準位置` のなす角が視線値 yaw, pitch となります。
`HeadBone の Forward` は、レスト時のHumanoidのHeadのワールド空間における+Z向きのベクトルを、Headのローカル空間で評価したものが用いられます。
yaw, pitch をアプリケーションが直接生成する場合は `LookAt` には基準位置は使われません。

また、アプリケーションによっては、モデルの一人称視点の位置の取得・反映にも用いられることがあります。
VR向けHMDの位置を想定しています。

基準位置は、Humanoidの `head` ボーンのローカル空間に `offsetFromHeadBone` を加えて評価します。

> Implementation note: モデルに `offsetFromHeadBone` が存在しない場合は、実装ごとに適切な値にフォールバックを行うことが推奨されます。

## 目の可動範囲の調整

視線値 yaw, pitch をモデルに適用するときに値を調整します。

* rangeMapHorizontalInner
* rangeMapHorizontalOuter
* rangeMapVerticalDown
* rangeMapVerticalUp

の４つで目の可動範囲を設定できます。

LookAtType が expression の場合は、 LookLeft, LookRight に対して `rangeMapHorizontalOuter` の値を使用します。
LookLeft, LookRight が両目をまとめて可動させるため、片方に `Inner` 反対に `Outer` というように適用することが不可能であるためです。

> このことより、LookAtTypeがexpressionの場合、寄り目等の表現はできません。

### 水平内側

`extensions.VRMC_vrm.lookAt.rangeMapHorizontalInner`

* 左目の右方向
* 右目の左方向
* boneタイプ: outputScale には leftEye, rightEye ボーンの Euler 角 (degree) による最大回転角度を指定します
* expressionタイプ: outputScale には LookLeft, LookRight expression の最大適用量を指定します(最大1.0)

```
Y = clamp(yaw, 0, rangeMapHorizontalInner.inputMaxValue)/rangeMapHorizontalInner.inputMaxValue * rangeMapHorizontalInner.outputScale
```

`expression` タイプでは使用されません。水平方向は `rangeMapHorizontalOuter` が使われます。

### 水平外側

`extensions.VRMC_vrm.lookAt.rangeMapHorizontalOuter`

* 左目の左方向
* 右目の右方向
* boneタイプ: outputScale には leftEye, rightEye ボーンの Euler 角 (degree) による最大回転角度を指定します
* expressionタイプ: outputScale には LookLeft, LookRight expression の最大適用量を指定します(最大1.0)

```
Y = clamp(yaw, 0, rangeMapHorizontalOuter.inputMaxValue)/rangeMapHorizontalOuter.inputMaxValue * rangeMapHorizontalOuter.outputScale
```

`expression` タイプでは水平方向として使われます。

### 垂直下方向

`extensions.VRMC_vrm.lookAt.rangeMapVerticalDown`

* 左目の下方向
* 右目の下方向
* boneタイプ: outputScale には leftEye, rightEye ボーンの Euler 角 (degree) による最大回転角度を指定します
* expressionタイプ: outputScale には LookLeft, LookRight expression の最大適用量を指定します(最大1.0)

```
Y = clamp(yaw, 0, rangeMapVerticalDown.inputMaxValue)/rangeMapVerticalDown.inputMaxValue * rangeMapVerticalDown.outputScale
```

### 垂直上方向

`extensions.VRMC_vrm.lookAt.rangeMapVerticalUp`

* 左目の上方向
* 右目の上方向
* boneタイプ: outputScale には leftEye, rightEye ボーンの Euler 角 (degree) による最大回転角度を指定します
* expressionタイプ: outputScale には LookLeft, LookRight Expression の最大適用量を指定します(最大1.0)

```
Y = clamp(yaw, 0, rangeMapVerticalUp.inputMaxValue)/rangeMapVerticalUp.inputMaxValue * rangeMapVerticalUp.outputScale
```

### Boneタイプの可動範囲の適用

可動範囲調整後の Yaw, Pitch 角の値をオイラー角として leftEye, rightEye ボーンの LocalRotation に適用します。

### Expressionタイプの可動範囲の適用

可動範囲調整後の Yaw, Pitch 角をExpressionのweight値として LookLeft, LookRight, LookDown, LookUp Expressionに適用します。

## LookAtのアルゴリズム

* HeadBoneのforwardベクトルと、HeadBone + firstPersonBoneOffset の位置から注視点を結んだベクトルが為す方向の Yaw Pitch 角を計算
* x, y = ClampAndScale(yaw, pitch)
* apply(x, y)

### Boneタイプ

| bone と yaw, pitch              | 備考                                                         |
|:--------------------------------|:-------------------------------------------------------------|
| leftEye + yaw(左)               | rangeMapHorizontalOuter を適用してオイラー角として反映します |
| leftEye + yaw(右)               | rangeMapHorizontalInner を適用してオイラー角として反映します |
| rightEye + yaw(左)              | rangeMapHorizontalInner を適用してオイラー角として反映します |
| rightEye + yaw(右)              | rangeMapHorizontalOuter を適用してオイラー角として反映します |
| leftEye or rightEye + pitch(下) | rangeMapVerticalDown を適用してオイラー角として反映します    |
| leftEye or rightEye + pitch(上) | rangeMapVerticalUp を適用してオイラー角として反映します      |

### Expressionタイプ

| bone と yaw, pitch              | 備考                                                                         |
|:--------------------------------|:-----------------------------------------------------------------------------|
| leftEye + yaw(左)               | rangeMapHorizontalOuter を適用して Expression LookLeft の値として反映します  |
| leftEye + yaw(右)               | rangeMapHorizontalOuter を適用して Expression LookRight の値として反映します |
| rightEye + yaw(左)              | rangeMapHorizontalOuter を適用して Expression LookLeft の値として反映します  |
| rightEye + yaw(右)              | rangeMapHorizontalOuter を適用して Expression LookRight の値として反映します |
| leftEye or rightEye + pitch(下) | rangeMapVerticalDown を適用して Expression LookDown の値として反映します     |
| leftEye or rightEye + pitch(上) | rangeMapVerticalUp を適用して Expression LookUp の値として反映します         |

LookAtのExpressionタイプは、 MorphTarget タイプと TextureUVOffset タイプがありますが、ここでの処理は同じです。
