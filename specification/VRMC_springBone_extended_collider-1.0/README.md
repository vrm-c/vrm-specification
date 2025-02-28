# VRMC_springBone_extended_collider-1.0

*Version 1.0*


<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [Overview](#overview)
  - [Extended Colliders](#extended-colliders)
    - [Inside Sphere Collider](#inside-sphere-collider)
    - [Inside Capsule Collider](#inside-capsule-collider)
    - [Plane Collider](#plane-collider)
- [glTF Schema Updates](#gltf-schema-updates)
  - [Extending Colliders](#extending-colliders)
  - [Exporter Implemantation](#exporter-implemantation)
    - [Fallback: Inside Sphere Collider /  Inside Capsule Collider](#fallback-inside-sphere-collider---inside-capsule-collider)
    - [Fallback: Plane Collider](#fallback-plane-collider)
  - [VRMC_springBone_extended_collider](#vrmc_springbone_extended_collider)
    - [Properties](#properties)
    - [JSON Schema](#json-schema)
    - [VRMC_springBone_extended_collider.specVersion ✅](#vrmc_springbone_extended_colliderspecversion-)
    - [VRMC_springBone_extended_collider.shape](#vrmc_springbone_extended_collidershape)
  - [Shape](#shape)
    - [Properties](#properties-1)
    - [JSON Schema](#json-schema-1)
    - [Shape.sphere](#shapesphere)
    - [Shape.capsule](#shapecapsule)
    - [Shape.plane](#shapeplane)
  - [ShapeSphere](#shapesphere)
    - [Properties](#properties-2)
    - [ShapeSphere.offset](#shapesphereoffset)
    - [ShapeSphere.radius](#shapesphereradius)
    - [ShapeSphere.inside](#shapesphereinside)
  - [ShapeCapsule](#shapecapsule)
    - [Properties](#properties-3)
    - [ShapeCapsule.offset](#shapecapsuleoffset)
    - [ShapeCapsule.radius](#shapecapsuleradius)
    - [ShapeCapsule.tail](#shapecapsuletail)
    - [ShapeCapsule.inside](#shapecapsuleinside)
  - [ShapePlane](#shapeplane)
    - [Properties](#properties-4)
    - [ShapePlane.offset](#shapeplaneoffset)
    - [ShapePlane.normal](#shapeplanenormal)
    - [Properties](#properties-5)
- [Appendix: Reference Implementations](#appendix-reference-implementations)
  - [Inside Sphere Collider](#inside-sphere-collider-1)
  - [Inside Capsule Collider](#inside-capsule-collider-1)
  - [Plane Collider](#plane-collider-1)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

- 0b5vr

## Status

Complete

## Dependencies

Written against the glTF 2.0 spec.

The specification depends on [`VRMC_springBone`](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_springBone-1.0/README.md).

## Overview

The spec `VRMC_springBone_extended_collider` is a glTF extension that extends the shape of the colliders defined in `VRMC_springBone`.
This extension defines the inside sphere collider, inside capsule collider, and plane collider.

The colliders added by this extension are used to restrict the domain of spring bones by collision, similar to the colliders defined in `VRMC_springBone`.

### Extended Colliders

The colliders added by this extension are the inside sphere collider, inside capsule collider, and plane collider.

For each collider, reference implementations are given in [Appendix: Reference Implementations](#appendix-reference-implementations).

#### Inside Sphere Collider

While the normal sphere collider restricts the spring bone outside the sphere, the inside sphere collider restricts the spring bone inside the sphere.

The inside sphere collider is defined by the position in the local space and the radius of the sphere, similar to the existing sphere collider.

#### Inside Capsule Collider

While the normal capsule collider restricts the spring bone outside the capsule, the inside capsule collider restricts the spring bone inside the capsule.

The inside capsule collider is defined by the head position in the local space, the tail position in the local space, and the radius of the capsule, similar to the existing capsule collider.

#### Plane Collider

The plane collider restricts the spring bone to one side of the plane.
The plane is defined as an infinite plane and has no finite size.

The plane collider is defined by the position and the normal of the plane in the local space.

## glTF Schema Updates

### Extending Colliders

The constraints are described by adding the `VRMC_springBone_extended_collider` extension to the colliders defined in `VRMC_springBone`.

```json
{
  "extensionsUsed": [
    "VRMC_springBone",
    "VRMC_springBone_extended_collider"
  ],
  "extensions": {
    "VRMC_springBone": {
      "specVersion": "1.0",
      "colliders": [
        {
          "node": 0,
          "shape": {
            "sphere": {
              "radius": 0.0,
              "offset": [0.0, -10000.0, 0.0]
            }
          },
          "extensions": {
            "VRMC_springBone_extended_collider": {
              "specVersion": "1.0",
              "shape": {
                "sphere": {
                  "radius": 0.5,
                  "offset": [0.0, 0.0, 0.0],
                  "inside": true
                }
              }
            }
          }
        },
        // ...
      ]
    }
  },
  // other glTF 2.0 informations
  "nodes": [
    // ...
  ]
}
```

### Exporter Implemantation

> *This section is non-normative. *

If a collider is defined in the `VRMC_springBone_extended_collider` extension, it is recommended to define a fallback collider in `VRMC_springBone` to ignore or approximate the collider defined in `VRM_springBone_extended_collider`.

#### Fallback: Inside Sphere Collider /  Inside Capsule Collider

When exporting a sphere/capsule collider defined as an inside collider, consider having a fallback value such as setting a sphere collider far away from the origin so that the fallback collider does not affect spring bones in environments that do not support `VRMC_springBone_extended_collider`.

Below is an example of outputting so that the fallback collider has no effect.

```json
    // Example of placing a sphere collider with a radius of 0 far away and making it pseudo-ignored in the fallback environment.
      "colliders": [
        {
          "node": 0,
          "shape": {
            "sphere": {
              "radius": 0.0,
              "offset": [0.0, -10000.0, 0.0]
            }
          },
          "extensions": {
            "VRMC_springBone_extended_collider": {
              "specVersion": "1.0-draft",
              "shape": {
                "sphere": {
                  "radius": 0.5,
                  "offset": [0.0, 0.0, 0.0],
                  "inside": true
                }
              }
            }
          }
        }
      ]
```

#### Fallback: Plane Collider

When exporting a plane collider, consider having a fallback value such as using a sphere collider with a large enough radius so that the fallback collider approximates the behavior of a plane collider in environments that do not support `VRMC_springBone_extended_collider`.

Below is an example of exporting a fallback collider to approximate a plane collider.

```json
    // Example of placing a sphere collider with a radius of 1000 and approximating a plane collider in a fallback environment.
    // The precision of float type is approximately 6 digits. 1000 was selected for 0.1mm accuracy.
      "colliders": [
        {
          "node": 0,
          "shape": {
            "sphere": {
              "radius": 1000.0,
              // Specify plane offset - normal * radius
              "offset": [0.0, -1000.0, 0.0]
            }
          },
          "extensions": {
            "VRMC_springBone_extended_collider": {
              "specVersion": "1.0-draft",
              "shape": {
                "palne": {
                  "offset": [0.0, 0.0, 0.0],
                  "normal": [0.0, 1.0, 0.0],
                }
              }
            }
          }
        }
      ]
```

### VRMC_springBone_extended_collider

The root object of this extension.

#### Properties

||Type|Description|Required|
|:-|:-|:-|:-|
|`specVersion`|`string`|The version of this extension|✅ Yes|
|`shape`|[Shape](#shape)|The shape of the collider|No|

#### JSON Schema

[VRMC_springBone_extended_collider.schema.json](schema/VRMC_springBone_extended_collider.schema.json)

#### VRMC_springBone_extended_collider.specVersion ✅

Specification version of `VRMC_springBone_extended_collider`.
The value MUST be `"1.0"`.

- Type: `string`
- Required: Yes

#### VRMC_springBone_extended_collider.shape

The shape of the collider.

- Type: [Shape](#shape)
- Required: No

### Shape

Defines the shape of the collider.

It MUST define one of [ShapeSphere](#shapesphere), [ShapeCapsule](#shapecapsule), or [ShapePlane](#shapeplane).

#### Properties

||Type|Description|Required|
|:-|:-|:-|:-|
|`sphere`|[ShapeSphere](#shapesphere)|A sphere collider|No|
|`capsule`|[ShapeCapsule](#shapecapsule)|A capsule collider|No|
|`plane`|[ShapePlane](#shapeplane)|A plane collider|No|

#### JSON Schema

[VRMC_springBone_extended_collider.shape.schema.json](schema/VRMC_springBone_extended_collider.shape.schema.json)

#### Shape.sphere

A sphere collider.

- Type: [ShapeSphere](#shapesphere)
- Required: No

#### Shape.capsule

A capsule collider.

- Type: [ShapeCapsule](#shapecapsule)
- Required: No

#### Shape.plane

A plane collider.

- Type: [ShapePlane](#shapeplane)
- Required: No

### ShapeSphere

Defines the shape of a sphere collider.

#### Properties

||Type|Description|Required|
|:-|:-|:-|:-|
|`offset`|`number[3]`|The offset of the sphere|No|
|`radius`|`number`|The radius of the sphere|No|
|`inside`|`boolean`|`true` if it is an inside collider|No|

#### ShapeSphere.offset

The offset of the sphere from the origin in the local space.

- Type: `number[3]`
- Required: No, default: [0.0, 0.0, 0.0]

#### ShapeSphere.radius

The radius of the sphere.

- Type: `number`
- Required: No, default: 0.0
- Minimum: 0.0

#### ShapeSphere.inside

If true, the collider prevents spring bones from going outside of the sphere instead.

- Type: `boolean`
- Required: No, default: `false`

### ShapeCapsule

Defines the shape of a capsule collider.

#### Properties

||Type|Description|Required|
|:-|:-|:-|:-|
|`offset`|`number[3]`|The offset of the capsule head|No|
|`radius`|`number`|The radius of the capsule|No|
|`tail`|`number[3]`|The offset of the capsule tail|No|
|`inside`|`boolean`|`true` if it is an inside collider|No|

#### ShapeCapsule.offset

The offset of the capsule head from the origin in the local space.

- Type: `number[3]`
- Required: No, default: [0.0, 0.0, 0.0]

#### ShapeCapsule.radius

The radius of the capsule.

- Type: `number`
- Required: No, default: 0.0
- Minimum: 0.0

#### ShapeCapsule.tail

The offset of the capsule tail from the origin in the local space.

- Type: `number[3]`
- Required: No, default: [0.0, 0.0, 0.0]

#### ShapeCapsule.inside

If true, the collider prevents spring bones from going outside of the capsule instead.

- Type: `boolean`
- Required: No, default: `false`

### ShapePlane

Defines the shape of a plane collider.

#### Properties

||Type|Description|Required|
|:-|:-|:-|:-|
|`offset`|`number[3]`|The offset of the plane|No|
|`normal`|`number[3]`|The normal of the plane|No|

#### ShapePlane.offset

The offset of the plane from the origin in the local space.

- Type: `number[3]`
- Required: No, default: [0.0, 0.0, 0.0]

#### ShapePlane.normal

The normal of the plane in the local space.
The vector MUST be normalized.

- Type: `number[3]`
- Required: No, default: [0.0, 0.0, 1.0]

#### Properties

## Appendix: Reference Implementations

> The following information is Non-normative.

The reference implementations of the colliders defined in this extension are shown below.

translate: Please refer to the reference implementation of the `VRMC_springBone` extension for the application of `distance` and `direction` calculated by the following reference implementation.

### Inside Sphere Collider

The following is a reference implementation of the inside sphere collider in pseudocode.

```ts
var transformedOffset = collider.offset * collider.worldMatrix;
var delta = nextTail - transformedOffset;

// The distance from the collider to the joint. A negative value indicates that they are colliding
var distance = collider.radius - jointRadius - delta.magnitude;

// The direction from the collider to the joint. If they are colliding, the joint will be pushed in this direction
var direction = -delta.normalized;
```

> Implementation Note: The only difference between the implementation of the inside sphere collider and the existing sphere collider is the distance calculation and direction calculation. The rest of the implementation can be reused from the existing sphere collider.

### Inside Capsule Collider

The following is a reference implementation of the inside capsule collider in pseudocode.

```ts
var transformedOffset = collider.offset * collider.worldMatrix;
var transformedTail = collider.tail * collider.worldMatrix;
var offsetToTail = transformedTail - transformedOffset;

var dot = dot(offsetToTail, delta);

var delta = nextTail - transformedOffset;

if (dot < 0.0) {
    // When the joint is at the head side of the capsule
    // Do nothing
} else if (dot > offsetToTail.sqMagnitude) {
    // When the joint is at the tail side of the capsule
    delta -= offsetToTail;
} else {
    // When the joint is between the head and tail of the capsule
    delta -= offsetToTail * (dot / offsetToTail.sqMagnitude);
}

// The distance from the collider to the joint. A negative value indicates that they are colliding
var distance = collider.radius - jointRadius - delta.magnitude;

// The direction from the collider to the joint. If they are colliding, the joint will be pushed in this direction
var direction = -delta.normalized;
```

> Implementation Note: The only difference between the implementation of the inside capsule collider and the existing capsule collider is the distance calculation and direction calculation. The rest of the implementation can be reused from the existing capsule collider.

### Plane Collider

The following is a reference implementation of the plane collider in pseudocode.

```ts
var transformedOffset = collider.offset * collider.worldMatrix;
var transformedNormal = (colliderNormal * normalMatrixFrom(collider.worldMatrix)).normalized;
var delta = nextTail - transformedOffset;

// The distance from the collider to the joint. A negative value indicates that they are colliding
var distance = dot(delta, transformedNormal) - jointRadius;

// The direction from the collider to the joint. If they are colliding, the joint will be pushed in this direction
var direction = transformedNormal;
```
