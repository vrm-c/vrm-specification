# About pose data compatibility

> This document is non-normative.

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
Subtle differences, such as being slightly slanted during T-pose, are considered as the same posture so that it won't be in exactly the same posture.

### pose data
**Pose data** is represented by translation values of the `hips` bone and local rotations of all Humanoid bones.

The values for the same posture will change under the influence of rest rotation.
Also, the values for the same posture will change depending on the presence or absence of non-required bones.

**If the rest rotation of T-pose or the presence or absence of non-essential bones are different, pose data for the same pose cannot be used as-is.**

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

- `TPoseA`: Model A rest rotation
- `PoseForA`: Pose data that gives the intended look when applied to model A
- `TPoseB`: Model B rest rotation

Given the above, we will explain how to obtain pose data `PoseForB` that looks the same as `PoseForA` when applied to Model B.

### intermediate format: NormalizedLocalRotation

To simplify processing here, we introduce an intermediate format: `NormalizedLocalRotation`.

`PoseForA` => `NormalizedLocalRotation` => `PoseForB`

`NormalizedLocalRotation` is pose data that looks the same as `PoseForA` when applied to a model with no rest rotation.

> The intermediate format is almost the same as the normalized state of VRM-0.X. The definition of T-pose was clarified in VRM-1.0, so it cannot be said that they are completely the same.
>
> If `TPoseA` has no rotation, `PoseForA` and `NormalizedLocalRotation` will be equal, making it easier.
> Similarly, if `TPoseB` has no rotation, `NormalizedLocalRotation` and `PoseForB` will be equal, making it easier.

### `PoseForA` => `NormalizedLocalRotation`

- W: World rest rotation of TPoseA
- L: Local rest rotation of TPoseA

$NormalizedLocalRotation = W \cdot L^{-1} \cdot A.LocalRotation \cdot W^{-1}$

```cs
// C#
InitialGlobalRotation * Quaternion.Inverse(InitialLocalRotation) * Transform.localRotation * Quaternion.Inverse(InitialGlobalRotation);
```

### `NormalizedLocalRotation` => `PoseForB`

- W: World rest rotation of TPoseB
- L: Local rest rotation of TPoseB

$B.LocalRotation = L \cdot W^{-1} \cdot NormalizedLocalRotation \cdot W$

```cs
// C#
ControlTarget.localRotation = _initialTargetLocalRotation * (Quaternion.Inverse(_initialTargetGlobalRotation) * ControlBone.localRotation * _initialTargetGlobalRotation);
```

### When the presence or absence of non-required bones differs
This section explains the conversion process when the presence or absence of non-required bones differs between the conversion source model and conversion destination model.

#### When the source model has few bones
If the source model has few bones, it is recommended to simply apply only the bones contained in the source model.

#### When the target model has few bones
If the destination model has few bones, it is recommended to apply the rotation of the target bone to all child bones that would otherwise be affected by the rotation of the target bone.
For example, if upperChest exists in the source model but not in the destination model, the neck, leftShoulder, and rightShoulder of the destination model should be transformed by both the rotation of upperChest and the rotation of the bone itself by multiplying the two.
In other words, we expect the visual posture of each bone in the source animation to be in the same orientation as the corresponding bone in the destination model.

### About translation scaling
Scaling the hips bone translation between models of different sizes can give a natural look.
One possible formula is as follows:

`scaling_factor = dst(T-Pose hips_height) / src(T-Pose hips_height)`

The idea is that between models where each bone has the same rotation, the amount of movement will be proportional to the leg length.
Consider the height of the hips as the length of the leg.
