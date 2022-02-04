# VRMC_node_constraint

*Version 1.0-draft*

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [Overview](#overview)
- [Constraints](#constraints)
  - [Sources](#sources)
  - [Constraint spaces](#constraint-spaces)
  - [Rotation Constraint](#rotation-constraint)
    - [Constrained Axes](#constrained-axes)
    - [Weight](#weight)
- [glTF Schema Updates](#gltf-schema-updates)
  - [Extending Nodes](#extending-nodes)
  - [VRMC_node_constraint](#vrmc_node_constraint)
    - [Properties](#properties)
    - [VRMC_node_constraint.specVersion ✅](#vrmc_node_constraintspecversion-)
    - [VRMC_node_constraint.constraint ✅](#vrmc_node_constraintconstraint-)
  - [constraint](#constraint)
    - [Properties](#properties-1)
    - [constraints.rotation ✅](#constraintsrotation-)
  - [rotationConstraint](#rotationconstraint)
    - [Properties](#properties-2)
    - [rotationConstraint.source ✅](#rotationconstraintsource-)
    - [rotationConstraint.axes](#rotationconstraintaxes)
    - [rotationConstraint.weight](#rotationconstraintweight)
- [Implementation Notes](#implementation-notes)
  - [Dependency resolution between constraints](#dependency-resolution-between-constraints)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

## Status

* Work in progress

## Dependencies

Written against the glTF 2.0 spec.

## Overview

This extension allows glTF to constrain the transform of a node by another node.

This extension defines Rotation Constraint.

## Constraints

A constraint can be **rotation constraint**.

### Sources

Each constraint specifies a source that will be used to be a reference transform when it constrains the destination node.

Nodes must meet these requirements to be a source:

- The source must not be the destination itself
- The source evaluated in model space (described in the core specification of VRM) must not be a child of the destination
- The source must not make a circular dependency between two or more constraints

### Constraint spaces

Source and destination of each constraint is evaluated in local space.

### Rotation Constraint

A rotation constraint constrains the rotation of a node by another node.

The rotation of the source and the destination will be evaluated relative from their initial rotations, meaning just applying the constraint without any rotation of the source does not do anything to the source.

> **TODO**: Rotation offset should be described more precisely

#### Constrained Axes

When an axis is specified by the property `axes`, the rotation of the axis will be constrained by the constraint.
If an axis is not specified, the constraint does not do anything to the orientation around the axis.

> **TODO**: How do we "ignore" the orientation? The implementation should be described

#### Weight

The final contribution to the rotation offset will be determined by slerp of identity quaternion toward rotation offset of the source where t is the weight.

---

## glTF Schema Updates

### Extending Nodes

A constraint will be described by adding a `VRMC_node_constraint` extension to a node.
For example, the following defines a constraint that constrains the rotation of the node `NodeB` by another node `NodeA`:

```json
{
  "nodes": [
    {
      "name": "NodeA",
    },
    {
      "name": "NodeB",
      "extensions": {
        "VRMC_node_constraint": {
          "specVersion": "1.0-draft",
          "constraint": {
            "rotation": {
              "source": 0,
              "weight": 1.0
            }
          }
        }
      }
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
The value will be `"1.0-draft"` .

- 型: `string`
- 必須: Yes

#### VRMC_node_constraint.constraint ✅

A [constraint](#constraint).

- Type: `object`
- Required: Yes

---

### constraint

The object that contains the constraint.

#### Properties

|            | Type     | Description            | Required |
|:-----------|:---------|:-----------------------|:---------|
| `rotation` | `object` | A rotation constraint. | ✅ Yes    |

- JSON schema: [VRMC_node_constraint.constraint.schema.json](./schema/VRMC_node_constraint.constraint.schema.json)

#### constraints.rotation ✅

A [rotation constraint](#rotationConstraint).

- Type: `object`
- Required: Yes

---

### rotationConstraint

A set of parameters of a rotation constraint can be used to constrain a rotation of a node by another node.

#### Properties

|              | Type         | Description                                             | Required                          |
|:-------------|:-------------|:--------------------------------------------------------|:----------------------------------|
| `source`     | `integer`    | The index of the node constrains the node.              | ✅ Yes                             |
| `axes`       | `boolean[3]` | Axes be constrained by this constraint, in X-Y-Z order. | No, default: `[true, true, true]` |
| `weight`     | `number`     | The weight of the constraint.                           | No, default: `1.0`                |

- JSON schema: [VRMC_node_constraint.rotationConstraint.schema.json](./schema/VRMC_node_constraint.rotationConstraint.schema.json)

#### rotationConstraint.source ✅

The index of the node constrains the node.

- Type: `integer`
- Required: Yes
- Minimum: `>= 0`

#### rotationConstraint.axes

Axes be constrained by this constraint, in X-Y-Z order.

- Type: `boolean[3]`
- Required: No, default: `[true, true, true]`

#### rotationConstraint.weight

The weight of the constraint.

- Type: `number`
- Required: No, default: `1.0`

## Implementation Notes

*This section is non-normative.*

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
