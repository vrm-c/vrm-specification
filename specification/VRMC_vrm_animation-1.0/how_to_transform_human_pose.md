# About pose data compatibility

> Non-normative

`VRMC_vrm_animation` handles pose data of humanoid models.
There is a pose data compatibility issue between models with different T-poses.
This document explains how to resolve pose data compatibility between models with different T-poses.

## Terms

### Humanoid
*Humanoid* referred to in this document depends on the VRM Humanoid definition.

[Humanoid definition within the VRM specification](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/humanoid.ja.md)

### T-pose
*T-pose* referred to in this document depends on T-pose of VRM.
[VRM T-pose: Specification of posture defined by VRM](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/tpose.ja.md)

### rest rotation
The rotation state of node in T-pose state defined by VRM-1.0 is **Rest rotation**.

VRM-0.X made pose data compatible by forcing rest rotations to be no rotations.
VRM-1.0 eliminates this no-rotation restriction on rest rotation.

### Non-required bone
**Non-required bones** in VRM may have different presence or absence between models.

For example, bones such as `hips` and `head` are required bones, whereas bones such as `upperChest` and `leftShoulder` are non-required bones.
Therefore, there are models with and without the `upperChest` bone.

### same posture
Suppose each `HumanBone` has the **same pose** when it is visually oriented the same way.
Subtle differences such as being slightly slanted during T-pose are included. It won't be in exactly the same position.

### pose data
**Pose data** is represented by translation values of the `hips` bone and local rotations of all Humanoid bones.

The values for the same posture will change under the influence of rest rotation.
Also, the values for the same posture will change depending on the presence or absence of non-required bones.

**If the rest rotation of T-pose or the presence or absence of non-essential bones are different, pose data for the same pose will not be compatible.**

## Conversion processing to make pose data compatible

In order to make pose data compatible between models with different T-poses, one of the following conversion processes is required.

### Convert the model
Converts the rest rotation of the model to no rotation.

In VRM-0.X (UniVRM), the rest rotation of the model was converted to no rotation when exporting the model.

### Convert pose data
Convert pose data to fit a model with arbitrary rest rotation.

In VRM-1.0, this method is assumed because the model has arbitrary rest rotation.

VRM-1.0 (UniVRM) provides a function called **ControlRig** that allows pose data conversion at runtime.

## Conversion process details

- `TPoseA`: モデルAのレスト回転
- `PoseForA`: モデルAに適用したときに意図通りの姿勢となるポーズデータ
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
