
# VRMC_node_shape

## Contributors

* TODO: Name, affiliation, and contact info for each contributor

## Status

Draft

## Dependencies

Written against the glTF 2.0 spec.

## Overview

Define the shape for the node.
For example, define the collision detection shape of `VRMC_springBone`.

```json
{
    "nodes":[
    {
        "extensions": {
            "VRMC_node_shape":{
                "shapes": [
                {
                    "sphere": {
                        "offset": [0, 0, 0],
                        "radius": 1
                    }
                },
                {
                    "capsule": {
                        "offset": [0, 0, 0],
                        "radius": 1,
                        "tail": [0, 0, 1]
                    }
                }]
            }
        }
    }]
}
```

### shapes

Array of shapes.

#### Sphere shape

| key   | type   | 備考                         |
|:------|:-------|:-----------------------------|
| offset| float3 | center position in node local|
| radius| float  | radius                       |

```json
{
    "sphere": {
        "offset": [0, 0, 0],
        "radius": 1
    }
}
```

#### Capsule shape

| key   | type   | 備考                                      |
|:------|:-------|:------------------------------------------|
| offset| float3 | capsule start position in node local      |
| radius| float  | radius(cylinder, hemispheres at both ends)|
| tail  | float3 | capsule end position in node local        |

```json
{
    "capsule": {
        "offset": [0, 0, 0],
        "radius": 1,
        "tail": [0, 0, 1]
    }
}
```
