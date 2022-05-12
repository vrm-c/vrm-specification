# VRMC_node_constraint

*Version 1.0-beta*

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [Overview](#overview)
  - [Purposes](#purposes)
- [Constraints](#constraints)
  - [Sources](#sources)
  - [Weight](#weight)
  - [Roll Constraint](#roll-constraint)
    - [Purposes](#purposes-1)
    - [Hierarchy](#hierarchy)
    - [Roll Axis](#roll-axis)
    - [Evaluation of rotations](#evaluation-of-rotations)
    - [Example of Implementation](#example-of-implementation)
  - [Aim Constraint](#aim-constraint)
    - [Purposes](#purposes-2)
    - [Hierarchy](#hierarchy-1)
    - [Aim Axis](#aim-axis)
    - [Evaluation of rotations](#evaluation-of-rotations-1)
    - [Example of Implementation](#example-of-implementation-1)
  - [Rotation Constraint](#rotation-constraint)
    - [Purposes](#purposes-3)
    - [Evaluation of rotations](#evaluation-of-rotations-2)
    - [Example of Implementation](#example-of-implementation-2)
- [glTF Schema Updates](#gltf-schema-updates)
  - [Extending Nodes](#extending-nodes)
  - [VRMC_node_constraint](#vrmc_node_constraint)
    - [Properties](#properties)
    - [VRMC_node_constraint.specVersion ✅](#vrmc_node_constraintspecversion-)
    - [VRMC_node_constraint.constraint ✅](#vrmc_node_constraintconstraint-)
  - [constraint](#constraint)
    - [Properties](#properties-1)
    - [constraint.roll](#constraintroll)
    - [constraint.aim](#constraintaim)
    - [constraint.rotation](#constraintrotation)
  - [rollConstraint](#rollconstraint)
    - [Properties](#properties-2)
    - [rollConstraint.source ✅](#rollconstraintsource-)
    - [rollConstraint.rollAxis ✅](#rollconstraintrollaxis-)
    - [rollConstraint.weight](#rollconstraintweight)
  - [aimConstraint](#aimconstraint)
    - [Properties](#properties-3)
    - [aimConstraint.source ✅](#aimconstraintsource-)
    - [aimConstraint.aimAxis ✅](#aimconstraintaimaxis-)
    - [aimConstraint.weight](#aimconstraintweight)
  - [rotationConstraint](#rotationconstraint)
    - [Properties](#properties-4)
    - [rotationConstraint.source ✅](#rotationconstraintsource-)
    - [rotationConstraint.weight](#rotationconstraintweight)
- [Implementation Notes](#implementation-notes)
  - [Dependency resolution between constraints](#dependency-resolution-between-constraints)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

* 進藤哲郎
* 蘇柏彰
* Obuchi Yutaka

## Status

* Work in progress

## Dependencies

Written against the glTF 2.0 spec.

## Overview

This extension allows glTF to constrain the transform of a node by another node.

This extension defines Roll Constraint, Aim Constraint, and Rotation Constraint.

### Purposes

The Constraint extension in VRM is designed for purposes that controls additional bones using transform informations applied to [humanoid bones](../VRMC_vrm-1.0-beta//humanoid.md) in realtime.

In addition, it defines constraints with semantical meaning considering the constraints are used across platforms.
We expect implementations to have enough features that artists need, and we do not aim to make the behavior completely identical between implementations of the extension.

## Constraints

A constraint can be either *Roll Constraint*, *Aim Constraint*, or *Rotation Constraint*.

### Sources

Each constraint specifies a *source* which will be a reference transform, and a *destination* node which is being constrained.

Nodes must meet these requirements to be a source:

- The source must not be the destination itself
- The source evaluated in model space (described in the core specification of VRM) must not be a child of the destination
- The source must not make a circular dependency between two or more constraints

### Weight

Each constraint specifies a *weight* that represents how much rotation is transferred to the destination.

Weight is a number in the range of [0.0 - 1.0] and performs Spherical Linear Interpolation (slerp) from the rest rotation of the destination to the rotation determined by the constraint.

### Roll Constraint

Roll Constraint constrains a rotation of a destination around a specified axis using a source.

#### Purposes

The Roll Constraint is intended to be used for the following purposes:

- Twist bones of arms and legs

#### Hierarchy

Roll Constraint assumes hierarchies like following for example:

```markdown
- LowerArm
  - Hand
  - Twist1 (RollConstraint, Source is LowerArm)
  - Twist2 (RollConstraint, Source is LowerArm)
```

#### Roll Axis

Roll Constraint specifies a *roll axis*, which indicates which axis of destination should transfer from the source.

Roll axis can be one of `"X"`, `"Y"`, or `"Z"`.

#### Evaluation of rotations

It **SHOULD** evaluate the rotation from the rest transform of the source to the current orientation of the source, around the roll axis of the rest transform of the destination.
Also, if the source is rotating outside of the roll axis, it **SHOULD** evaluate the roll axis using the difference between a minimum rotation which makes the roll axis direct at the same direction.

#### Example of Implementation

> *This section is non-normative.*

This is the example implementation written in a pseudocode:

```js
deltaSrcQuat = srcRestQuat.inverse * srcQuat
deltaSrcQuatInParent = srcRestQuat * deltaSrcQuat * srcRestQuat.inverse // source to parent
deltaSrcQuatInDst = dstRestQuat.inverse * deltaSrcQuatInParent * dstRestQuat // parent to destination

toVec = rollAxis.applyQuaternion( deltaSrcQuatInDst )
fromToQuat = Quaternion.fromToRotation( rollAxis, toVec )

targetQuat = Quaternion.slerp(
  dstRestQuat,
  dstRestQuat * fromToQuat.inverse * deltaSrcQuatInDst,
  weight
)
```

### Aim Constraint

Roll Constraint constrains a rotation of a destination to make it aim towards a source.

#### Purposes

The Roll Constraint is intended to be used for the following purposes:

- Sleeves of clothes

#### Hierarchy

Roll Constraint assumes hierarchies like following for example:

```markdown
- UpperArm
  - LowerArm
- Aim (AimConstraint, Source is LowerArm)
```

#### Aim Axis

Aim Constraint specifies a *aim axis*, which indicates which axis of destination should aim at the source.

Aim axis can be one of `"PositiveX"`, `"NegativeX"`, `"PositiveY"`, `"NegativeY"`, `"PositiveZ"`, or `"NegativeZ"`.

#### Evaluation of rotations

It **SHOULD** evaluate the rotation from the rest transform of the destination to the orientation that makes the aim axis aim at the source in world coordinates.
The rotation **SHOULD** be the smallest possible rotation that meets the condition.

#### Example of Implementation

> *This section is non-normative.*

This is the example implementation written in a pseudocode:

```js
fromVec = aimAxis.applyQuaternion( dstParentWorldQuat * dstRestQuat )
toVec = ( srcWorldPos - dstWorldPos ).normalized
fromToQuat = Quaternion.fromToRotation( fromVec, toVec )

targetQuat = Quaternion.slerp(
  dstRestQuat,
  dstParentWorldQuat.inverse * fromToQuat * dstParentWorldQuat * dstRestQuat,
  weight
)
```

### Rotation Constraint

Rotation Constraint constrains a rotation of a destination using a rotation of a source.

This extension defines the Rotation Constraint in Local-Local.

#### Purposes

The Rotation Constraint is intended to be used for the following purposes:

- Sub arm

#### Evaluation of rotations

It **SHOULD** evaluate the rotation from the rest transform of the source to the current rotation of the source in the local coordinate of the source, then transfer to the destination in the local coordinate of the destination.

> It expects the same behavior as Bone Constraint, Local-Local Copy Rotation implemented in Blender.

#### Example of Implementation

> *This section is non-normative.*

This is the example implementation written in a pseudocode:

```js
srcDeltaQuat = srcRestQuat.inverse * srcQuat

targetQuat = Quaternion.slerp(
  dstRestQuat,
  dstRestQuat * srcDeltaQuat,
  weight
)
```

---

## glTF Schema Updates

### Extending Nodes

A constraint will be described by adding a `VRMC_node_constraint` extension to a node.
For example, the following defines a Rotation Constraint that constrains the rotation of the node `NodeB` by another node `NodeA`:

```json
{
  "extensionsUsed": {
    "VRMC_node_constraint"
  },
  "nodes": [
    {
      "name": "NodeA",
    },
    {
      "name": "NodeB",
      // node.extensions
      "extensions": {
        "VRMC_node_constraint": {
          "specVersion": "1.0-beta",
          "constraint": {
            "rotation": {
              "source": 0,
              "weight": 1.0
            }
          }
        }
      }
    }
  ],
  // other glTF 2.0 informations
  "materials": [
    {
      // ...
    }
  ]
}
```

---

### VRMC_node_constraint

The root object of the extension.

#### Properties

|               | Type     | Description                                    | Required |
|:--------------|:---------|:-----------------------------------------------|:---------|
| `specVersion` | `string` | The version of VRMC_node_constraint extension. | ✅ Yes    |
| `constraint`  | `object` | An object that contains a constraint.          | ✅ Yes    |

- JSON schema: [VRMC_node_constraint.schema.json](./schema/VRMC_node_constraint.schema.json)

#### VRMC_node_constraint.specVersion ✅

Represents the version of VRMC_node_constraint extension.
The value will be `"1.0-beta"` .

- Type: `string`
- Required: Yes

#### VRMC_node_constraint.constraint ✅

A [constraint](#constraint).

- Type: `object`
- Required: Yes

---

### constraint

The object that contains the constraint.

It **MUST** have one of either `roll`, `aim`, or `rotation`.

#### Properties

|            | Type     | Description            | Required |
|:-----------|:---------|:-----------------------|:---------|
| `roll`     | `object` | A Roll Constraint.     | No       |
| `aim`      | `object` | An Aim Constraint.     | No       |
| `rotation` | `object` | A Rotation Constraint. | No       |

- JSON schema: [VRMC_node_constraint.constraint.schema.json](./schema/VRMC_node_constraint.constraint.schema.json)

#### constraint.roll

A [Roll Constraint](#rollConstraint).

- Type: `object`
- Required: No

#### constraint.aim

An [Aim Constraint](#aimConstraint).

- Type: `object`
- Required: No

#### constraint.rotation

A [Rotation Constraint](#rotationConstraint).

- Type: `object`
- Required: No

---

### rollConstraint

An object describes a set of parameters of a [Roll Constraint](#roll-constraint).

#### Properties

|            | Type      | Description                               | Required           |
|:-----------|:----------|:------------------------------------------|:-------------------|
| `source`   | `integer` | The index of the node constrains the node | ✅ Yes              |
| `rollAxis` | `string`  | The roll axis of the constraint           | ✅ Yes              |
| `weight`   | `number`  | The weight of the constraint              | No, default: `1.0` |

- JSON schema: [VRMC_node_constraint.rollConstraint.schema.json](./schema/VRMC_node_constraint.rollConstraint.schema.json)

#### rollConstraint.source ✅

The index of the node constrains the node.

- Type: `integer`
- Required: Yes
- Minimum: `>= 0`

#### rollConstraint.rollAxis ✅

The roll axis of the constraint.

- Type: `string`
- Required: Yes
- Allowed values:
  - `X`
  - `Y`
  - `Z`

#### rollConstraint.weight

The weight of the constraint.

- Type: `number`
- Required: No, default: `1.0`

---

### aimConstraint

An object describes a set of parameters of a [Aim Constraint](#aim-constraint).

#### Properties

|           | Type      | Description                               | Required           |
|:----------|:----------|:------------------------------------------|:-------------------|
| `source`  | `integer` | The index of the node constrains the node | ✅ Yes              |
| `aimAxis` | `string`  | The aim axis of the constraint            | ✅ Yes              |
| `weight`  | `number`  | The weight of the constraint              | No, default: `1.0` |

- JSON schema: [VRMC_node_constraint.aimConstraint.schema.json](./schema/VRMC_node_constraint.aimConstraint.schema.json)

#### aimConstraint.source ✅

The index of the node constrains the node.

- Type: `integer`
- Required: Yes
- Minimum: `>= 0`

#### aimConstraint.aimAxis ✅

The aim axis of the constraint.

- Type: `string`
- Required: Yes
- Allowed values:
  - `PositiveX`
  - `NegativeX`
  - `PositiveY`
  - `NegativeY`
  - `PositiveZ`
  - `NegativeZ`

#### aimConstraint.weight

The weight of the constraint.

- Type: `number`
- Required: No, default: `1.0`

---

### rotationConstraint

An object describes a set of parameters of a [Rotation Constraint](#rotation-constraint).

#### Properties

|          | Type      | Description                               | Required           |
|:---------|:----------|:------------------------------------------|:-------------------|
| `source` | `integer` | The index of the node constrains the node | ✅ Yes              |
| `weight` | `number`  | The weight of the constraint              | No, default: `1.0` |

- JSON schema: [VRMC_node_constraint.rotationConstraint.schema.json](./schema/VRMC_node_constraint.rotationConstraint.schema.json)

#### rotationConstraint.source ✅

The index of the node constrains the node.

- Type: `integer`
- Required: Yes
- Minimum: `>= 0`

#### rotationConstraint.weight

The weight of the constraint.

- Type: `number`
- Required: No, default: `1.0`

---

## Implementation Notes

> *This section is non-normative.*

### Dependency resolution between constraints

A constraint often depends on other constraints.
Constraints should be updated in correct orders under cases that has dependencies, to prevent constraints from referring transforms that is not updated yet.

The pseudocode is an example of procedure how constraints should be updated:

```
let constraintsPending = empty set of Constraint
let constraintsDone = empty set of Constraint

function updateConstraint( constraint: Constraint )
  if not constraintsDone.has( constraint ) then
    if constraintPending.has( constraint ) then
      throw "Circular dependency detected"
    end if

    constraintsPending.add( constraint )
    foreach dependency in constraint.dependencies do
      updateConstraint( constraint )
    end foreach
    constraintsPending.delete( constraint )

    constraint.update()

    constraintsDone.add( constraint )
  end if
end function

function updateConstraints
  foreach constraint in constraints do
    updateConstraint( constraint )
  end foreach
end function
```
