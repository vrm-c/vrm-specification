# HumanPose の変換方法について

本ドキュメントでは HumanPose のローカルローテーションを扱います。

## 用語

### TPose
VRM-1.0 の TPose に依存します。... を参照してください。

### 初期回転
VRM-1.0の定義する TPose 状態での node の回転状態です。
local と world の両方を利用します。

### ポーズ
hips の移動と HumanBone のローカル回転の配列で表現するとします。

## 概要

２つのヒューマノイドヒエラルキーの
TPose のときの各ノードの回転状態が異なっている場合、
２つのヒューマノイド間で同じ `ポーズ` を共有することはできません。

- TPoseA
- PoseForA
- TPoseB

があるときに、`PoseForB` を得る方法を説明します。

## 中間形式 NormalizedLocalRotation

ここで処理を単純化するために中間形式 NormalizedLocalRotation を導入します。

`PoseForA` => `NormalizedLocalRotation` => `PoseForB`

NormalizedLocalRotation は、TPose のときに初期回転を持たないヒエラルキーに対する
ポーズであるとします。

> `VRM-0.X` の正規化状態とほぼ同じです。VRM-1.0でTPoseの定義を明確化したので、 同一とは言い切れませんが。
> BVH で初期ポーズが TPose であるものとも互換性があります。BVH は仕様で初期姿勢を決めていないので、 移動スケールが cm である、初期姿勢が TPose でない、などバリエーションがあります。

> `TPoseA` が正規化済みの場合は `PoseForA` と`NormalizedLocalRotation`  が等しくなり簡単になります
> `TPoseB` が正規化済みの場合は `NormalizedLocalRotation` と `PoseForB` が等しくなり簡単になります

## `PoseForA` => `NormalizedLocalRotation`

- W: TPoseA の World 初期回転
- L: TPoseA の Local 初期回転

$NormalizedLocalRotation = W \cdot L^{-1} \cdot A.LocalRotation \cdot W^{-1}$

```cs
InitialGlobalRotation * Quaternion.Inverse(InitialLocalRotation) * Transform.localRotation * Quaternion.Inverse(InitialGlobalRotation);
```

## `NormalizedLocalRotation` => `PoseForB`

- W: TPoseB の World 初期回転
- L: TPoseB の Local 初期回転

$B.LocalRotation = L \cdot W^{-1} \cdot NormalizedLocalRotation \cdot W$

```cs
ControlTarget.localRotation = _initialTargetLocalRotation * (Quaternion.Inverse(_initialTargetGlobalRotation) * ControlBone.localRotation * _initialTargetGlobalRotation);
```

## bone の不一致

必須ボーン、非必須ボーン共に一致しない場合があります。

> HumanBone しかない中間形式で吸収するのが楽そうです。
> 不要なボーンを除去し、不足したボーンを Identity とします

以下の方法で算出してください。

```
BoneLocalRotation = Inverse(ParentBoneWorldRotation) * LocalWorldRotation
```

実際のヒエラルキーの parent ではなく Humanoid の parent を参照してください。
hips は、ModelRoot を parent としてください。

