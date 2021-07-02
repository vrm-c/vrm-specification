# VRMC_springBone

*Version 1.0-draft*

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [Overview](#overview)
  - [`VRMC_springBone.specVersion`](#vrmc_springbonespecversion)
  - [`VRMC_springBone.colliders`](#vrmc_springbonecolliders)
  - [`VRMC_springBone.colliderGroups`](#vrmc_springbonecollidergroups)
  - [`VRMC_springBone.springs`](#vrmc_springbonesprings)
    - [About spring configuration](#about-spring-configuration)
    - [joints](#joints)
  - [`VRMC_springBone.springs[*].joints[*]`](#vrmc_springbonespringsjoints)
- [SpringBone Algorithm](#springbone-algorithm)
  - [Inertia calculation](#inertia-calculation)
  - [Collision detection](#collision-detection)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

* Shindo Tatsuro
* Po-Chang Su
* Obuchi Yutaka

## Status

Draft
## Dependencies

Written against the glTF 2.0 spec.
## Overview

A simple physics-style animation system.
It is intended for use with the appearance of shaking hair and costumes.
It implements inertial animation on a set of configured nodes (SpringBone).
You can adjust the behavior with parameters such as rigidity, deceleration, and gravity.
You can also set the collision between the end (sphere) of each section of SpringBone and the collision detection node (collider: sphere / capsule).

```json
{
    "extensionsUsed": [
        "VRMC_springBone"
    ],
    "extensions": {
        "VRMC_springBone": {
            "specVersion": "1.0-draft",
            "colliders": [
                {
                    "node": 2,
                    "shape": {
                        "sphere": {
                            "offset": [0, 0, 0],
                            "radius": 1
                        }
                    },
                },
                {
                    "node": 2,
                    "shape": {
                        "capsule": {
                            "offset": [0, 0, 0],
                            "radius": 1,
                            "tail": [
                                0,
                                0,
                                1
                            ]
                        }
                    }
                }
            ],
            "colliderGroups": [
                {
                    // group0
                    "name": "group0",
                    "colliders": [0, 1]
                },
            ],
            "springs": [
                {
                    "joints": [
                        {
                            "node": 0 // node0
                        },
                        {
                            "node": 1 // node1
                        }
                    ],
                    "colliderGroups": [
                        0,
                    ]
                }
            ]
        }
    },
    "nodes": [
        {
            "name": "node0",
        },
    ]
}
```

### `VRMC_springBone.specVersion`

Represents the version of the spec of VRMC_springBone extension.

```json
extensions.VRMC_springBone.specVersion = "1.0-draft"
```

### `VRMC_springBone.colliders`

Defines collision detection for SpringBone.
The target node and its shape.

```json
{
    "extensions": {
        "VRMC_springBone": {
            "colliders": [
                {
                    "node": 1,
                    "shape": {
                        "sphere": {
                            "offset": [0, 0, 0],
                            "radius": 1
                        }
                    },
                },
                {
                    "node": 1,
                    "shape": {
                        "offset": [0, 0, 0],
                        "radius": 1,
                        "tail": [0, 0, 1]
                    },
                }
            ]
        }
    }
}            
```

shape is exclusive with either `sphere` or` capsule`.

| key                  | type    | Remarks                                                                                                                               |
|:---------------------|:--------|:--------------------------------------------------------------------------------------------------------------------------------------|
| node                 | integer | Target node                                                                                                                           |
| shape.sphere.offset  | float3  | Only if shape is a sphere: the center position of the sphere in the local coordinates of the target node                              |
| shape.sphere.radius  | float   | only if shape is a sphere: radius of the sphere                                                                                       |
| shape.capsule.offset | float3  | Only if the shape is a capsule: the center position of the capsule start point semicircle in the local coordinates of the target node |
| shape.capsule.radius | float   | Only if the shape is a capsule: the radius of the semicircle and cylinder of the capsule                                              |
| shape.capsule.tail   | float3  | Only if the shape is a capsule: the center position of the capsule end-side semicircle in the local coordinates of the target node    |
| Name                 | Note    |                                                                                                                                       |

### `VRMC_springBone.colliderGroups`

```json
{
    "extensions": {
        "VRMC_springBone": {
            "colliderGroups": [
                {
                    "name": "groupName",
                    "colliders": [0, 1, 2]
                }
            ]
        }
    }
}
```

| key       | type       | Remarks                                                               |
|:----------|:-----------|:----------------------------------------------------------------------|
| name      | string     | group name                                                            |
| colliders | integer [] | List of indexes for VRMC_springBone.colliders in the previous section |

### `VRMC_springBone.springs`

```json
{
    "extensions": {
        "VRMC_springBone": {
            "springs": [
                {
                    "name": "spring0",
                    "joints": [
                    ],
                    "colliderGroups": [0],
                }
            ]
        }
    }
}
```

| Name           | Remarks                                                                                   |
|:---------------|:------------------------------------------------------------------------------------------|
| name           | Spring name                                                                               |
| joints         | List of joints that make up springBone                                                    |
| colliderGroups | His list of indexes for `VRMC_springBone.colliderGroups` that collide against this spring |

#### About spring configuration

```
a-b-c-d
```

If there are joints, Runtime interprets them as follows.

```
jointNode (rotate)
| +-collision
v v
a-b
b-c
c-d
```

Expanded to three joints.
The joint parameters specified for the end node are not interpreted.
If you want to move d, add an empty node at the end.

```
jointNode
| +-collision
v v
a-b
b-c
c-d
d-e
```

For `vrm0`

```
a-b-c-d
```

If there are joints, `vrm0` interprets it as follows.

```
jointNode (rotate)
| +-collision
v v
a-b
b-c
c-d
d- (Add a 7cm fixed collision detection at the end)
```

#### joints

joints is a set of nodes that have a continuous parent-child relationship.
There are the following restrictions.

* joints [n-1] must be the parent or ancestor of joints [n]

If joints [n-1] and joints [n] are not direct parent-child nodes, the nodes in between are ignored.
If the end of joints is not the terminal node, then the descendant nodes will be ignored and will not sway individually.

> As explained above, by not setting joints, you can skip mid- or terminal nodes and set them to sway. However, if that node has no other purpose, it's redundant and it's a good idea to remove it altogether.

### `VRMC_springBone.springs[*].joints[*]`

```json
{
    "extensions": {
        "VRMC_springBone": {
            "springs": [
                {
                    "joints": [
                        {
                            "node": 0,
                            "hitRadius": 0.1,
                            "stiffness": 0.5,
                            "gravityPower": 1.0,
                            "gravityDir": [0, -1, 0],
                            "dragForce": 0.5,
                        },
                        {
                            "node": 1,
                            // The joint at the end is not required except for node.
                        }
                    ]
                }
            ]
        }
    }
}
```

| Name         | Value         | Remarks                                                  |
|:-------------|:--------------|:---------------------------------------------------------|
| node         | integer       | index of the target node                                 |
| hitRadius    | float (meter) | SpringBone hitbox size                                   |
| stiffness    | [0-1]         | Rigidity (force to return to the initial state)          |
| gravityPower |               | Gravity force (force applied to Spring Bone every frame) |
| gravityDir   | [x, y, z]     | Gravity direction                                        |
| dragForce    | [0-1]         | Deceleration (force to decelerate Spring Bone)           |

## SpringBone Algorithm

### Inertia calculation

```cs
// Calculate the next position with verlet integral
var nextTail = currentTail
     + (currentTail --prevTail) * (1.0f --dragForce) // Continue moving the previous frame (with attenuation)
     + ParentRotation * m_localRotation * m_boneAxis * stiffnessForce // Goal of child bone movement due to parent rotation
     + external // Amount of movement due to external force
     ;
```

### Collision detection

TODO:

```cs
// If you judge the hit judgment and the collision and collide, push it away to the point where it does not collide
```
