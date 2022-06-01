# VRMC_vrm

*Version 1.0-beta*

## Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [Extensions used along with VRMC_vrm](#extensions-used-along-with-vrmc_vrm)
- [Limitation of KHR_texture_transform](#limitation-of-khr_texture_transform)
  - [Obsolete properties of KHR_texture_transform with VRM1](#obsolete-properties-of-khr_texture_transform-with-vrm1)
- [Overview](#overview)
  - [JSON Schema](#json-schema)
  - [Version of VRMC_vrm specification](#version-of-vrmc_vrm-specification)
  - [Format and Extension](#format-and-extension)
- [glTF schema Updates](#gltf-schema-updates)
  - [Coordinate Units](#coordinate-units)
  - [Unused Items](#unused-items)
    - [Allow ignore stored TANGENT](#allow-ignore-stored-tangent)
    - [`meshes[*].primitives[*].attributes.TANGENT`](#meshesprimitivesattributestangent)
    - [`meshes[*].primitives[*].targets.TANGENT`](#meshesprimitivestargetstangent)
  - [`meshes[*].extras.targetNames` names of morphTargets (recommendation)](#meshesextrastargetnames-names-of-morphtargets-recommendation)
- [`VRMC_vrm.humanoid` Assignment to humanoid bones (required)](#vrmc_vrmhumanoid-assignment-to-humanoid-bones-required)
- [`VRMC_vrm.meta` Model information (required)](#vrmc_vrmmeta-model-information-required)
- [`VRMC_vrm.firstPerson` First person (optional)](#vrmc_vrmfirstperson-first-person-optional)
- [Expression, LookAt, SpringBone, Constraints application order](#expression-lookat-springbone-constraints-application-order)
- [`VRMC_vrm.expressions` Facial expressions (optional)](#vrmc_vrmexpressions-facial-expressions-optional)
- [`VRMC_vrm.lookAt` Eye control (optional)](#vrmc_vrmlookat-eye-control-optional)
- [Known Implementations](#known-implementations)
- [Resources](#resources)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

* Shindo Tatsuro
* Hirose Junmichi
* Po-Chang Su
* Obuchi Yutaka

## Status

* In development

## Dependencies

Written against the glTF 2.0 spec.

## Extensions used along with VRMC_vrm

VRMC_vrm extension is intended to be used along with these extensions.

* KHR_materials_unlit
* KHR_texture_transform
* KHR_materials_emissive_strength
* VRMC_materials_mtoon
* VRMC_springBone
* VRMC_node_constraint

## Limitation of KHR_texture_transform

(KHR_texture_transform)[https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_texture_transform/README.md] can manipulate `offset`, `rotation`, `sacle` and `texCoord` for each [textureInfo](https://github.com/KhronosGroup/glTF/blob/master/specification/2.0/schema/textureInfo.schema.json) of each Material.

For the glTF PBR Material,

* pbrMetallicRoughness.baseColorTexture(only this if KHR_materials_unlit is enabled)
* pbrMetallicRoughness.metallicRoughnessTexture
* normalTexture
* occlusionTexture
* emissiveTexture

### Obsolete properties of KHR_texture_transform with VRM1

There is the renderer implementation that cannot manipulate all KHR_texture_transform for texture settings of materials.
Then, we not recommend to use below properties of KHR_texture_transform.

* rotation
* texCoord

## Overview

glTF represent all of Scene,
But VRM represent a Humanoid for VR avatar.

### Model Space

VRM defines a "model space" for each VRM model, that observes relative transforms from the root of the glTF scene.
This is distinct from a world space defined in the application that uses the VRM model.

Model space will be used in [`VRMC_node_constraint`](../VRMC_node_constraint-1.0_draft/README.md) extension.

> To move VRM models in an application,
> It expects to move the root of the glTF scene, not only the Hips bone defined in Humanoid.
> In other words, you should not leave the root of the model to an origin of world space.

### JSON Schema

```js
{
  "extensionsUsed": {
    "VRMC_vrm"
  },
  "extensions": {
    "VRMC_vrm": {
      // VRM extension
      "specVersion": "1.0-beta",
      "humanoid": {},
      "meta": {},
      "firstPerson": {},
      "expression": {},
      "lookAt": {},
    },
    "VRMC_springBone": {},
    "VRMC_node_constraint": {}
  },
  // glTF-2.0
  "materials": [
    "extensions": {
      "VMRC_materials_mtoon": {}
    }
  ],
}
```

* https://github.com/vrm-c/vrm-specification/tree/master/specification/VRMC_vrm-1.0_draft/schema

JSON Schema of glTF 2.0

* https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/schema

### Version of VRMC_vrm specification

```json
extensions.VRMC_vrm.specVersion  = "1.0-beta"
```

### Format and Extension

Saved in `.glb` format and used `.vrm` as the extension.

## glTF schema Updates

### Coordinate Units

Metric units based on glTF [coordinate-system-and-units](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#coordinate-system-and-units).

### Unused Items

The following items are not used:

* animations
* cameras

#### Allow ignore stored TANGENT

Because it is difficult to handle the tangent correctly,
Allow do not export and Calculate without reading.

#### `meshes[*].primitives[*].attributes.TANGENT`

* import: calculate using MikkTSpace algorithm.

https://github.com/KhronosGroup/glTF/blob/master/specification/2.0/README.md#meshes

> Implementation note: When tangents are not specified, client implementations should calculate tangents using default MikkTSpace algorithms. For best results, the mesh triangles should also be processed using default MikkTSpace algorithms.

* export: do not export, expecting to calculate with the algorithm.

#### `meshes[*].primitives[*].targets.TANGENT`

* morphTarget tangent is not recommended
* export: do not export
* import: do not import

### `meshes[*].extras.targetNames` names of morphTargets (recommendation)

Since there is no `meshes[*].primitives[*].targets.name`, `meshes[*].extras.targetNames` .

* https://github.com/KhronosGroup/glTF/issues/1036

## `VRMC_vrm.humanoid` Assignment to humanoid bones (required)

Assign a `part of the human body` (humanoid bone) to glTF.Node to define a humanoid model.

The specifications are shown in another document.

[./humanoid.md](./humanoid.md)

## `VRMC_vrm.meta` Model information (required)

The specifications are shown in another document.

[./meta.md](./meta.md)

## `VRMC_vrm.firstPerson` First person (optional)

VRM defines the setting of the first-person viewpoint assuming VR.

The specifications are shown in another document.

[./firstPerson.md](./firstPerson.md)

## Expression, LookAt, SpringBone, Constraints execution order

* VRMC_vrm.lookAt
* VRMC_vrm.expression
* VRMC_node_constraint
* VRMC_springBone

These components affect Node and Mesh, and the execution order matters.
The recommended order of execution in an update loop is as follows.

1. Resolve humanoid bones
2. Resolve LookAt as the position of the head is determined
   * Bone type => leftEye, rightEye Rotate bones
   * Expression type => Next section
3. Expression Update
   * Emotions and sorrows controller, etc. External input => Set Expression weight
   * LipSync => Set Expression weight
   * AutoBlink => Set Expression weight
   * Expression type LookAt => Set expression weight
4. Apply Expression
5. Resolve constraints
6. Resolve Spring Bone

## `VRMC_vrm.expressions` Facial expressions (optional)

VRM defines Expressions for humanoids.

> The word BlendShape used in the VRM-0 specification refers to the same thing as MorphTarget and has a different meaning, so we renamed it from BlendShape to Expression.

The specifications are shown in another document.

[./expressions.md](./expressions.md)

## `VRMC_vrm.lookAt` Eye control (optional)

VRMs define gaze control for humanoids.

The specifications are shown in another document.

[./lookAt.md](./lookAt.md)

## Known Implementations

* https://vrm.dev/vrm_applications/

## Resources

* https://vrm.dev/
