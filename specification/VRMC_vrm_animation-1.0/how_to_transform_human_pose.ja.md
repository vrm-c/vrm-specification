# HumanPose の互換性について

`VRMC_vrm_animation` では、ヒューマノイドのポーズを扱います。
このときに、`異なる T-pose` 間でのデータ表現に互換性の問題が発生する場合があります。
本ドキュメントでは、`異なる T-pose` 間でのポーズ互換性を解決する方法を説明します。

## 概要(異なる T-pose とは)

### T-pose
VRM-1.0 の T-pose に依存します。
[VRM T-pose: VRM が定義する姿勢の仕様](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/tpose.ja.md)を参照してください。

### レスト回転
VRM-1.0の定義する T-pose 状態での node の回転状態です。

* VRM-0.X ではレスト回転が 0 であることを仕様化することでポーズの互換性問題が発生しないようにしていました。[VRMの規約](https://github.com/vrm-c/vrm-specification/blob/master/specification/0.0/README.ja.md#vrm%E3%81%AE%E8%A6%8F%E7%B4%84)
* VRM-1.0 ではこのレスト回転の制約を解除しています。

### 姿勢(見た目)
各 `HumanBone` が同じ向きであるときに `同じ姿勢` であるとします。

* T-pose 時に少し斜めになっているなどの微妙な差異は含まれるため、完全に同じ角度にはなりません

### ポーズ(数値表現)
ポーズの数値表現は、`hips bone` の移動値とすべての `HumanBone` のローカル回転で表現されます。

* レスト回転の影響を受けて、同じ姿勢に対する数値が変化します。
* 非必須ボーンの有無の影響を受けて、同じ姿勢に対する数値が変化します。

**T-poseのレスト回転もしくは非必須ボーンの有無が違う場合、同じ姿勢に対するポーズは互換性がありません。**

## 解決策

ポーズの互換性に対応するためなんらかの変換が必要です。
処理タイミング、

* 事前 import/export
* Runtime

処理対象

* モデル
* ポーズ

などで、選択肢があります。

### モデルを変換する(レスト回転の無いT-pose)
`VRM-0.x` がこのアプローチです。
model のエクスポート時に変換します。
runtime はシンプルになります。

### ポーズを変換する(狭義のリターゲット)
`vrm-1.0` が想定する方法です。
runtime にポーズを変換します。
import 時にレスト姿勢から変換システムを構築する必要があります。

* UniVRM では ControlRig として RunTime ポーズ変換が提供されています

## 詳細(変換方法)

- TPoseA
- PoseForA
- TPoseB

があるときに、`PoseForB` を得る方法を説明します。

### 中間形式 NormalizedLocalRotation

ここで処理を単純化するために中間形式 NormalizedLocalRotation を導入します。

`PoseForA` => `NormalizedLocalRotation` => `PoseForB`

NormalizedLocalRotation は、TPose のときにレスト回転が0のヒエラルキーに対する
ポーズであるとします。

> `VRM-0.X` の正規化状態とほぼ同じです。VRM-1.0でTPoseの定義を明確化したので、 同一とは言い切れません。
> BVH で初期ポーズが TPose であるものとも互換性があります。BVH は仕様で初期姿勢を決めていないので、 移動スケールが cm である、初期姿勢が TPose でない、などバリエーションがあります。

> `TPoseA` が正規化済みの場合は `PoseForA` と`NormalizedLocalRotation`  が等しくなり簡単になります
> `TPoseB` が正規化済みの場合は `NormalizedLocalRotation` と `PoseForB` が等しくなり簡単になります

### `PoseForA` => `NormalizedLocalRotation`

- W: TPoseA の World レスト回転
- L: TPoseA の Local レスト回転

$NormalizedLocalRotation = W \cdot L^{-1} \cdot A.LocalRotation \cdot W^{-1}$

```cs
// C#
InitialGlobalRotation * Quaternion.Inverse(InitialLocalRotation) * Transform.localRotation * Quaternion.Inverse(InitialGlobalRotation);
```

### `NormalizedLocalRotation` => `PoseForB`

- W: TPoseB の World レスト回転
- L: TPoseB の Local レスト回転

$B.LocalRotation = L \cdot W^{-1} \cdot NormalizedLocalRotation \cdot W$

```cs
// C#
ControlTarget.localRotation = _initialTargetLocalRotation * (Quaternion.Inverse(_initialTargetGlobalRotation) * ControlBone.localRotation * _initialTargetGlobalRotation);
```

### 非必須ボーンの有無の不一致

以下の方法で算出してください。

```
BoneLocalRotation = Inverse(ParentBoneWorldRotation) * LocalWorldRotation
```

実際のヒエラルキーの parent ではなく Humanoid の parent を参照してください。
hips は、ModelRoot を parent としてください。
