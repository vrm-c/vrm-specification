# `VRMC_vrm.firstPerson`

This document provides specifications for the `firstPerson` field of the` VRMC_vrm` extension.

## MeshAnnotation

Control renderings in Mesh units.

`extensions.VRMC_vrm.firstPerson.meshAnnotations[*]`

| Name            | Note                                  |
|:----------------|:--------------------------------------|
| node            | Index for target node that has a mesh |
| firstPersonFlag | Described below                       |

firstPersonFlag. When using a model in the VR application, the renderings from the HMD and the others are separated.

## MeshAnnotation (enum)

`thirdPersonOnly` is for hiding the rendering of the model's head portion in VR. Since we assumed that the VR camera's viewpoint is at the head position, by setting thirdPersonOnly, the situation where the user may inadvertently see the model's face, head and hair (block the first-person view) can be prevented.

`firstPersonOnly` is a setting that objects rendered by the first-person camera can only be seen from the first-person view. Normally `firstPersonOnly` will not be used unless the application side needs it for special purposes.

Set `both` for objects that do not necessarily being rendered separately so that those objects can be rendered by all the cameras.

`auto` is used to divide the mesh into the head portion (`thirdPersonOnly`) and the others (`both`). See the details in the next section.

| Name            | First Person Camera (VR viewpoint) | Other Cameras     | Example                                                                      |
|:----------------|:-----------------------------------|:------------------|------------------------------------------------------------------------------|
| thirdPersonOnly | Rendering disable                  | Rendering enable  | Objects that block VR view such as face, eyes, head, hair, hat, helmet, etc. |
| firstPersonOnly | Rendering enable                   | Rendering disable | User interface that is only visible from the user's perspective view         |
| both            | Rendering enable                   | Rendering enable  |                                                                              |
| auto            |                                    |                   | Will describe later                                                          |

### MeshAnnotation.Auto Algorithm

* For a mesh specified as `auto`, each vertex will be checked whether it contains the weight of Head bone (or the child of the Head bone)
* Split the mesh into two groups: the first group contains triangles with HeadBone-related vertices while the second group contains triangles with the rest of the vertices
* Set the mesh containing the HeadBone-related vertices as `thirdPersonOnly`. And another mesh is set as `both`

### When MeshAnnotation does not exist

When you use the features of FirstPerson while there are meshes that do not have corresponding MeshAnnotation,
You must assume MeshAnnotation for the meshes are `auto`.

Also, when the `firstPerson` property itself does not exist in the VRM extension,
You must assume all meshes are annotated as `auto`.
