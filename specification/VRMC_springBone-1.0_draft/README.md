# VRMC_springBone

## Contributors

* TODO: Name, affiliation, and contact info for each contributor

## Status

Draft

## Dependencies

Written against the glTF 2.0 spec.

## Overview

The VRM defines the swaying object that does not rely on the physical engine.
It aims for creating the appearance of spring-physics applied hair and clothes.

| Name           | Note               |
|:---------------|:-------------------|
| boneGroups     | SpringBone list    |
| colliderGroups | ColliderGroup list |

### SpringBone

| Name           | Note                                                                         |
|:---------------|:-----------------------------------------------------------------------------|
| comment        | A character string (any)                                                     |
| stiffness      | The force to return to the initial pose                                      |
| gravityPower   | Gravitational acceleration                                                   |
| gravityDir     | The direction of gravity. A gravity other than downward direction also works |
| dragForce      | Air resistance. Deceleration force                                           |
| center         | The index of the node to be the local coordinate                             |
| hitRadius      | The radius of the sphere used for the collision detection with colliders     |
| bones          | The node index array to set SpringBone                                       |
| colliderGroups | Process collision detection. colliderGroup index                             |

### ColliderGroup

| Name      | Note                                  |
|:----------|:--------------------------------------|
| node      | The index of Node with collider setup |
| colliders | Collider list                         |

### Collider

* For use by SpringBone only and independent of the Physics system.

| Name      | Note                                                                 |
|:----------|:---------------------------------------------------------------------|
| shapeType | Collider shape (sphere, capsule)                                     |
| offset    | The offset from the collider's node                                  |
| rotation  | The local rotation from the collider's node. Euler Angle (Radians)   |
| size      | Collider radius. {radians} for sphere, {radians, length} for capsule |

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
