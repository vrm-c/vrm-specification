# VRMC_springBone

## Contributors

* TODO: Name, affiliation, and contact info for each contributor

## Status

Draft

## Dependencies

Written against the glTF 2.0 spec.

* Require VRMC_node_collider extension

## Overview

The VRM defines the swaying object that does not rely on the physical engine.
It aims for creating the appearance of spring-physics applied hair and clothes.

| Name     | Note               |
|:---------|:-------------------|
| settings | SpringSetting list |
| springs  | Spring list        |

### Setting

| Name         | Note                                                                         |
|:-------------|:-----------------------------------------------------------------------------|
| stiffness    | The force to return to the initial pose                                      |
| gravityPower | Gravitational acceleration                                                   |
| gravityDir   | The direction of gravity. A gravity other than downward direction also works |
| dragForce    | Air resistance. Deceleration force                                           |

### Collider

see VRMC_node_collider

### ColliderGroup

| Name       | Note                                                                                        |
|:-----------|:--------------------------------------------------------------------------------------------|
| name       | spring name                                                                                 |
| setting    | The index of setting                                                                        |
| rootSpring | The index of node that start spring                                                         |
| hitRadius  | The radius of the sphere used for the collision detection with colliders                    |
| colliders  | The node of detect collision with spring. This node should has VRMC_node_collider extension |

### SpringBone Algorithm

TODO:

```cs
// Calculate the next position with Verlet Integration
var nextTail = currentTail
    + (currentTail - prevTail) * (1.0f - dragForce) // Continue moving from the previous frame (with attenuation)
    + ParentRotation * m_localRotation * m_boneAxis * stiffnessForce // Moving target of child bone by parent rotation
    + external // Translation by external force
    ;
```
