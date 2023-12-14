# ポーズデータの互換性について

> 本ドキュメントは Non-normative です。

`VRMC_vrm_animation` では、人型モデル (Humanoid) のポーズデータを扱います。
このときに、異なる T-pose を持ったモデル間でのポーズデータの互換性の問題が発生する場合があります。
本ドキュメントでは、異なる T-pose を持ったモデル間でのポーズデータの互換性を解決する方法を説明します。

## 用語の導入

### Humanoid
本ドキュメントで扱う *Humanoid* は、VRM の Humanoid 定義に依存します。
[VRM 仕様内の Humanoid の定義](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/humanoid.ja.md)を参照してください。

### T-pose
本ドキュメントで扱う *T-pose* は、VRM の T-pose に依存します。
[VRM T-pose: VRM が定義する姿勢の仕様](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/tpose.ja.md)を参照してください。

### レスト回転
VRM-1.0の定義する T-pose 状態での node の回転状態を **レスト回転** と呼称します。

VRM-0.X では、レスト回転が無回転であることを仕様化することでポーズデータの互換性の問題が発生しないようにしていました。
VRM-1.0 ではこのレスト回転の制約を撤廃しています。

### 非必須ボーン
VRM で定義された Humanoid ボーンのうち、必須でない **非必須ボーン** は、モデル間で存在の有無が異なる場合があります。

例えば、 `hips` や `head` などのボーンは必須ボーンであるのに対して、 `upperChest` や `leftShoulder` などは非必須ボーンです。
このため、`upperChest` ボーンがあるモデルとないモデルの両方が存在します。

### 同じ姿勢
各 `HumanBone` が見た目に同じ向きであるときに **同じ姿勢** であるとします。
T-pose 時に少し斜めになっているなどの微妙な差異は含まれるため、完全に同じ姿勢にはなりません。

### ポーズデータ
**ポーズデータ** は、`hips` ボーンの平行移動値とすべての Humanoid ボーンのローカル回転で表現されます。

レスト回転の影響を受けて、同じ姿勢に対する数値が変化します。
また、非必須ボーンの有無の影響を受けて、同じ姿勢に対する数値が変化します。

**T-poseのレスト回転もしくは非必須ボーンの有無が違う場合、同じ姿勢に対するポーズデータは互換性がありません。**

## ポーズデータの互換性を持たせる変換処理

異なる T-pose を持ったモデル間でポーズデータの互換性を持たせるため、以下いずれかの変換処理が必要です。

### モデルを変換する
モデルのレスト回転を無回転に変換します。

VRM-0.X の UniVRM においては、モデルのエクスポート時にモデルのレスト回転を無回転にして出力していました。

### ポーズデータを変換する
ポーズデータを、任意のレスト回転を持ったモデルに対して適用できるように変換します。

VRM-1.0 においては、モデルが任意のレスト回転を持っているため、こちらの方法が想定されます。

VRM-1.0 の UniVRM においては、ランタイムでポーズデータの変換が行えるよう、 ControlRig という機能が提供されています。

## 変換処理の詳細

- `TPoseA`: モデルAのレスト回転
- `PoseForA`: モデルAに適用したときに意図した見た目となるポーズデータ
- `TPoseB`: モデルBのレスト回転

があるときに、`PoseForB`: モデルBに適用したときに `PoseForA` と同じ姿勢となるポーズデータ を得る方法を説明します。

### 中間形式 NormalizedLocalRotation

ここで処理を単純化するために、中間形式 `NormalizedLocalRotation` を導入します。

`PoseForA` => `NormalizedLocalRotation` => `PoseForB`

`NormalizedLocalRotation` は、レスト回転が無回転のモデルに対して適用したとき、 `PoseForA` と同じ姿勢となるポーズデータとします。

> VRM-0.X の正規化状態とほぼ同じです。 VRM-1.0 で T-pose の定義を明確化したので、同一とは言い切れません。
>
> `TPoseA` が正規化済みの場合は `PoseForA` と`NormalizedLocalRotation` が等しくなり簡単になります。
> 同様に、 `TPoseB` が正規化済みの場合は `NormalizedLocalRotation` と `PoseForB` が等しくなり簡単になります。

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

### 非必須ボーンの有無が異なる場合
変換元のモデル・変換先のモデルの間で、非必須ボーンの有無が異なる場合の変換処理について説明します。

#### 変換元のモデルのボーンが少ない場合
変換元のモデルのボーンが少ない場合、単純に変換元のモデルに含まれるボーンのみを適用することが推奨されます。

#### 変換先のモデルのボーンが少ない場合
変換先のモデルのボーンが少ない場合、本来対象ボーンの回転によって影響を与えるはずだった子ボーンすべてに対して、対象ボーンの回転を適用することが推奨されます。
例えば、upperChestが変換元のモデルに存在し変換先のモデルに存在しない場合、変換先のモデルのneck・leftShoulder・rightShoulderには、upperChestの回転とそのボーン自体の回転の両方を乗算して適用することが望ましいです。
すなわち、変換元のアニメーションにおけるボーンごとの見た目の姿勢と、変換先のモデルにおける対応するボーンの見た目の姿勢が同じ向きになることを期待します。
