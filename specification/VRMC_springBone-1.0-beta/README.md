# VRMC_springBone

*Version 1.0-beta*

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [Overview](#overview)
- [Structure](#structure)
  - [Terms](#terms)
    - [SpringJoint](#springjoint)
    - [SpringJointPair](#springjointpair)
    - [SpringChain](#springchain)
  - [Example](#example)
  - [For `vrm0`](#for-vrm0)
  - [Exception](#exception)
    - [Multiple SpringChains must not be duplicated in the same SpringJoint (prohibited)](#multiple-springchains-must-not-be-duplicated-in-the-same-springjoint-prohibited)
    - [Branching SpringChain (undefined)](#branching-springchain-undefined)
- [JSON](#json)
  - [`VRMC_springBone.specVersion`](#vrmc_springbonespecversion)
  - [`VRMC_springBone.colliders`](#vrmc_springbonecolliders)
  - [`VRMC_springBone.colliderGroups`](#vrmc_springbonecollidergroups)
  - [`VRMC_springBone.springs`](#vrmc_springbonesprings)
    - [joints](#joints)
  - [`VRMC_springBone.springs[*].joints[*]`](#vrmc_springbonespringsjoints)
- [SpringBone Algorithm](#springbone-algorithm)
  - [Calculation order](#calculation-order)
  - [Initialization](#initialization)
  - [Update procedure](#update-procedure)
    - [Inertia calculation](#inertia-calculation)
    - [Collision with colliders](#collision-with-colliders)
    - [Applying rotation](#applying-rotation)
  - [About center space](#about-center-space)

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

Implements a procedural animation of the spring that tries to maintain velocity by inertia and return to the node's original attitude.
It is intended for use with the appearance of shaking hair and costumes.
You can adjust the behavior with parameters such as rigidity, deceleration, and gravity.
You can also set the collision between the end (sphere) of each section of SpringBone and the collision detection node (collider: sphere / capsule).

## Structure

### Terms
The following terms are introduced for explanation.

| 用語                                      | 意味                 | json                                             |
|-------------------------------------------|----------------------|--------------------------------------------------|
| SpringJoint                               | node with settings   | `springs[i].joints[j]`                           |
| HeadSpringJoint and TailSpringJoint pair  | Two consecutive joint pairs | `springs[i].joints[j]`, `springs[i].joints[j+1]` |
| SpringChain                               | The whole continuous joint  | `springs[i]`                                     |

#### SpringJoint

`glTF` node.

#### SpringJointPair

A section represented by a pair of Head (`SpringJoint`) and Tail (`SpringJoint`).
Calculate the position difference from the previous frame with Tail.
It also calculates collisions with collisions, if set.
Head must always be an ancestor of Tail,
It does not have to be the parent of the Head.

```
  Sways
<- Tail ->
    |
    |
    o
   Head
```

Calculate the rotation of the Head from the movement of the Tail.

#### SpringChain

A chain of sets consisting of a series of consecutive `SpringJoint`s.

```
a-b-c-d
```

### Example

```
a-b-c-d
```

If there are joints, Runtime interprets them as follows.

```
Head(rotate)
| +-Tail(Movement difference / collision)
v v
a-b
b-c
c-d
```

Expands to three `HeadSpringJoint and TailSpringJoint pairs`.
The SpringJoint at the end is only used as a Tail, so no SpringJoint parameters are used.
If you want to rotate d, add Node to the end and set SpringJoint as shown below.
The Node you add can be empty.

```
Head(rotate)
| +-Tail(Movement difference / collision)
v v
a-b
b-c
c-d
d-e
```

### For `vrm0`

```
a-b-c-d
```

If there are joints, `vrm0` interprets it as follows.

```
Head(rotate)
| +-Tail(Movement difference / collision)
v v
a-b
b-c
c-d
d- (Add SpringJoint at a distance of 7 cm at the end)
```

### Exception

#### Multiple SpringChains must not be duplicated in the same SpringJoint (prohibited)

Individual `SpringChains` must not contain the same joint.
In the following cases, b, c and d belong to two SpringChains at the same time.

```
b-c-d

a-b-c-d
```

Configurations such as the following are implicitly considered duplicates.
b and c belong to two SpringChains at the same time.
Even if it is skipped, it will be subject to duplicate check.

```
() is skipped node
a-(b)-c

b-(c)-e
```

#### Branching SpringChain (undefined)
- Treat as separate `SpringBoneChain`

```
  x-y-z
  |
a-b-c-d
```

* Treat as two SpringChains, `a-b-c-d` and` x-y-z`.
* It is undefined without specifying which of `a-b-c-d` and` x-y-z` to process first, and when to obtain the movement difference. The behavior may differ depending on the implementation. Implementation convenience such as parallel execution may be prioritized.

## JSON

```json
{
    "extensionsUsed": [
        "VRMC_springBone"
    ],
    "extensions": {
        "VRMC_springBone": {
            "specVersion": "1.0-beta",
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
extensions.VRMC_springBone.specVersion = "1.0-beta"
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

#### joints

joints is a set of nodes that have a continuous parent-child relationship.
There are the following restrictions.

* joints [n] must be a parent or ancestor of joints[n+1]

If joints [n] and joints [n+1] are not direct parent-child nodes, the nodes in between are ignored.
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

> *This section is non-normative.*

This section explains the reference implementation of SpringBone.

Actual implementations can be seen in source codes of VRM implementations like UniVRM and three-vrm.

The reference implementation does a simple physics simulation using verlet integration.

### Calculation order

The update of the SpringBone system is done while resolving the dependencies between SpringBoneJoints.
Specifically, if a joint exists at the ancestors of a joint, that ancestors will be updated first.
In other words, the update process is performed from the root to the descendants.

### Initialization

In this implementation of SpringBoneJoint, a single joint has following states:

```ts
interface SpringBoneJointState {
    prevTail: Vector3;
    currentTail: Vector3;
    boneAxis: Vector3;
    boneLength: number;
    initialLocalRotation: Quaternion;
}
```

`prevTail` and `currentTail` represents a position of the specified children, in the world coordinate.
`currentTail` represents the current position, `prevTail` represents the position in the previous frame.

`boneAxis` represents the direction of the specified children in its rest state, in the local coordinate.
`boneLength` represents the length of the specified children, in the world coordinate.

`initialLocalRotation` represents the rest orientation of the specified joint node.

### Update procedure

The outline of the update procedure is presented in the following 3 steps:

- Inertia calculation
- Collision with colliders
- Applying rotation

#### Inertia calculation

Update the position of `currentTail` by calculating these three forces:

- Inertia: The force which the tail tries to move by inertia. `1.0 - dragForce` is the coefficient.
- Stiffness: The force which the tail tries to return to its original orientation. `stiffnessForce` is the coefficient.
- Gravity: The force which the tail is pulled by gravity. `gravityDir * gravityPower`

The pseudocode represents the procedure:

```ts
var {currentTail, prevTail, boneAxis, boneLength} = state;
var {dragForce, stiffnessForce, gravityDir, gravityPower} = props;

var worldPosition = node.worldPosition;
var localRotation = node.rotation;
var parentWorldRotation = node.parent ? node.parent.worldRotation : Quaternion.identity;

// calculate the next tail position using verlet integration
var intertia = (currentTail - prevTail) * (1.0f - dragForce);
var stiffness = deltaTime * parentWorldRotation * localRotation * boneAxis * stiffnessForce;
var external = deltaTime * gravityDir * gravityPower;

var nextTail = currentTail + intertia + stiffness + external;

// constrain the length
nextTail = worldPosition + (nextTail - worldPosition).normalized * boneLength;
```

#### Collision with colliders

Perform collision detection with colliders the joint specifies.
Calculate the distance between a collider and a joint.
If the distance is smaller than the sum of radii of the collider and the joint, it moves the joint to the position that pushes the joint by the collider, touching each other.

The pseudocode represents the procedure:

```ts
for (var collider of colliders) {
    var {dir, dist} = collider.calculateCollision(nextTail);

    if (dist < 0.0) {
        // push
        nextTail = nextTail - dir * dist;

        // constrain the length
        nextTail = worldPosition + (nextTail - worldPosition).normalized * boneLength;
    }
}
```

#### Applying rotation

Update the rotation of specified node using the `currentTail` we have calculated in procedures above.

The pseudocode represents the procedure:

```ts
// update prevTail and currentTail
prevTail = currentTail;
currentTail = nextTail;

var worldPosition = node.worldPosition;
var worldMatrix = node.worldMatrix;

// update rotation
var from = (boneAxis.applyMatrix4(worldMatrix) - worldPosition).normalized;
var to = (nextTail - worldPosition).normalized;

node.rotation = initialLocalRotation * Quaternion.fromToQuaternion(from, to);
```

### About center space

SpringBone implementations often adopt the concept of "Center" which is used to make the behavior of the SpringBone system relative to a specified transform.
The behavior can be achieved by calculating world space positions and matrices in space relative to the specified center.
