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
    - [Fallback: Inside Sphere Collider /  Inside Sphere Collider](#fallback-inside-sphere-collider---inside-sphere-collider)
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

glTF 2.0仕様に向けて策定されています。

本仕様は [`VRMC_springBone`](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_springBone-1.0/README.ja.md) に依存します。

## Overview

`VRMC_springBone_extended_collider` 仕様は、 `VRMC_springBone` で定義されたコライダーの形状を追加するglTF拡張です。
本拡張は、球の内部コライダー・カプセルの内部コライダー・平面コライダーを定義します。

本拡張によって追加されるコライダーは、 `VRMC_springBone` で定義されるコライダーと同様に、スプリングボーンの可動範囲を当たり判定によって制限するために使用されます。

### Extended Colliders

本拡張によって追加されるコライダーは、球の内部コライダー・カプセルの内部コライダー・平面コライダーの3種類です。

各コライダーについて、参考実装を[Appendix: Reference Implementations](#appendix-reference-implementations)に示します。

#### Inside Sphere Collider

通常の球コライダーがスプリングボーンを球の外側に制限するのに対して、球の内部コライダーはスプリングボーンを球の内部に制限します。

球の内部コライダーは、通常の球コライダーと同様に、ローカル座標系における位置と半径で定義されます。

#### Inside Capsule Collider

通常のカプセルコライダーがスプリングボーンをカプセルの外側に制限するのに対して、カプセルの内部コライダーはスプリングボーンをカプセルの内部に制限します。

カプセルの内部コライダーは、通常のカプセルコライダーと同様に、ローカル座標系における始点の位置・終点の位置・半径で定義されます。

#### Plane Collider

平面コライダーは、スプリングボーンが平面の片側に制限します。
平面は無限平面として定義され、有限の大きさを持ちません。

平面コライダーは、ローカル座標系における位置と法線ベクトルで定義されます。

## glTF Schema Updates

### Extending Colliders

コンストレイントは、 `VRMC_springBone` で定義されたコライダーに `VRMC_springBone_extended_collider` 拡張を追加することで記述されます。

```json
{
  "extensionsUsed": {
    "VRMC_springBone",
    "VRMC_springBone_extended_collider"
  },
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
  // 通常のglTF 2.0の情報
  "nodes": [
    // ...
  ]
}
```

### Exporter Implemantation

> *このセクションはnon-normativeです。*

`VRMC_springBone_extended_collider` 拡張でコライダーが定義されている場合、 `VRMC_springBone_extended_collider` に対応していない環境において適切にフォールバック処理がされるよう、 `VRMC_springBone` で定義されたコライダーには無視もしくは近似されるような値を出力することを推奨します。

#### Fallback: Inside Sphere Collider /  Inside Capsule Collider

内部コライダーとなる球・カプセルコライダーが定義されたファイルを出力する際、 `VRMC_springBone_extended_collider` に対応しない環境において、フォールバックのコライダーが影響を及ぼさないよう、位置を原点から遠く離した球コライダーとするなどの回避策を検討してください。

以下に、フォールバックのコライダーが影響を及ぼさないように出力する場合の例を示します。

```json
    // 遠方に半径0の球コライダーを配置し、フォールバック環境において擬似的に無視されるようにする例
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

平面コライダーが定義されたファイルを出力する際、 `VRMC_springBone_extended_collider` に対応しない環境において、フォールバックのコライダーでも平面コライダーに近い挙動を実現できるよう、半径を十分に大きくした球コライダーとするなどの回避策を検討してください。

以下に、フォールバックのコライダーで平面コライダーを近似するように出力する場合の例を示します。

```json
    // 半径1000の球コライダーを配置し、フォールバック環境において平面コライダーを近似する例
    // float 型の精度は約6桁。0.1mm 精度ということで1000にしています。
      "colliders": [
        {
          "node": 0,
          "shape": {
            "sphere": {
              "radius": 1000.0,
              // plane の offset - normal * radius を指定してください
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

本拡張のルートオブジェクトです。

#### Properties

||型|説明|必須|
|:-|:-|:-|:-|
|`specVersion`|`string`|この拡張のバージョン|✅ Yes|
|`shape`|[Shape](#shape)|コライダーの形状|No|

#### JSON Schema

[VRMC_springBone_extended_collider.schema.json](schema/VRMC_springBone_extended_collider.schema.json)

#### VRMC_springBone_extended_collider.specVersion ✅

`VRMC_springBone_extended_collider` 拡張のバージョンを示します。値は `"1.0"` でなければなりません。

- 型: `string`
- 必須: Yes

#### VRMC_springBone_extended_collider.shape

コライダーの形状を定義します。

- 型: [Shape](#shape)
- 必須: No

### Shape

コライダーの形状を定義します。

[ShapeSphere](#shapesphere)、[ShapeCapsule](#shapecapsule)、[ShapePlane](#shapeplane) のいずれか1つを含まなければいけません。

#### Properties

||型|説明|必須|
|:-|:-|:-|:-|
|`sphere`|[ShapeSphere](#shapesphere)|球コライダー|No|
|`capsule`|[ShapeCapsule](#shapecapsule)|カプセルコライダー|No|
|`plane`|[ShapePlane](#shapeplane)|平面コライダー|No|

#### JSON Schema

[VRMC_springBone_extended_collider.shape.schema.json](schema/VRMC_springBone_extended_collider.shape.schema.json)

#### Shape.sphere

球コライダーを定義します。

- 型: [ShapeSphere](#shapesphere)
- 必須: No

#### Shape.capsule

カプセルコライダーを定義します。

- 型: [ShapeCapsule](#shapecapsule)
- 必須: No

#### Shape.plane

平面コライダーを定義します。

- 型: [ShapePlane](#shapeplane)
- 必須: No

### ShapeSphere

球コライダーの形状を定義します。

#### Properties

||型|説明|必須|
|:-|:-|:-|:-|
|`offset`|`number[3]`|ローカル座標系における球の中心位置|No|
|`radius`|`number`|球の半径|No|
|`inside`|`boolean`|`true` の場合、内部コライダー|No|

#### ShapeSphere.offset

ローカル座標系における球の中心位置を示します。

- 型: `number[3]`
- 必須: No, 初期値: [0.0, 0.0, 0.0]

#### ShapeSphere.radius

球の半径を示します。

- 型: `number`
- 必須: No, 初期値: 0.0
- 最小値: 0.0

#### ShapeSphere.inside

`true` の場合、内部コライダーとして扱います。

- 型: `boolean`
- 必須: No, 初期値: `false`

### ShapeCapsule

カプセルコライダーの形状を定義します。

#### Properties

||型|説明|必須|
|:-|:-|:-|:-|
|`offset`|`number[3]`|ローカル座標系におけるカプセルの始点位置|No|
|`radius`|`number`|カプセルの半径|No|
|`tail`|`number[3]`|ローカル座標系におけるカプセルの終点位置|No|
|`inside`|`boolean`|`true` の場合、内部コライダー|No|

#### ShapeCapsule.offset

ローカル座標系におけるカプセルの始点位置を示します。

- 型: `number[3]`
- 必須: No, 初期値: [0.0, 0.0, 0.0]

#### ShapeCapsule.radius

カプセルの半径を示します。

- 型: `number`
- 必須: No, 初期値: 0.0
- 最小値: 0.0

#### ShapeCapsule.tail

ローカル座標系におけるカプセルの終点位置を示します。

- 型: `number[3]`
- 必須: No, 初期値: [0.0, 0.0, 0.0]

#### ShapeCapsule.inside

`true` の場合、内部コライダーとして扱います。

- 型: `boolean`
- 必須: No, 初期値: `false`

### ShapePlane

平面コライダーの形状を定義します。

#### Properties

||型|説明|必須|
|:-|:-|:-|:-|
|`offset`|`number[3]`|ローカル座標系における平面の位置|No|
|`normal`|`number[3]`|ローカル座標系における平面の法線ベクトル|No|

#### ShapePlane.offset

ローカル座標系における平面の位置を示します。

- 型: `number[3]`
- 必須: No, 初期値: [0.0, 0.0, 0.0]

#### ShapePlane.normal

ローカル座標系における平面の法線ベクトルを示します。
正規化されていなければなりません。

- 型: `number[3]`
- 必須: No, 初期値: [0.0, 0.0, 1.0]

#### Properties

## Appendix: Reference Implementations

> 以下の情報はNon-normativeです。

以下に、本拡張で定義するコライダーの参考実装を示します。

以下のリファレンス実装により計算した `distance` と `direction` の適用について、 `VRMC_springBone` 拡張のリファレンス実装も合わせて参照してください。

### Inside Sphere Collider

以下は、擬似コードによる球の内部コライダーの参考実装です。

```ts
var transformedOffset = collider.offset * collider.worldMatrix;
var delta = nextTail - transformedOffset;

// ジョイントとコライダーの距離。負の値は衝突していることを示す
var distance = collider.radius - jointRadius - delta.magnitude;

// ジョイントとコライダーの距離の方向。衝突している場合、この方向にジョイントを押し出す
var direction = -delta.normalized;
```

> Implementation Note: 内部コライダーでない通常の球コライダーとの実装の差異は、距離の計算および方向の計算の部分のみです。他の箇所について、通常の球コライダーの実装を流用することができます。

### Inside Capsule Collider

以下は、擬似コードによるカプセルの内部コライダーの参考実装です。

```ts
var transformedOffset = collider.offset * collider.worldMatrix;
var transformedTail = collider.tail * collider.worldMatrix;
var offsetToTail = transformedTail - transformedOffset;

var dot = dot(offsetToTail, delta);

var delta = nextTail - transformedOffset;

if (dot < 0.0) {
    // ジョイントがカプセルの始点側にある場合
    // なにもしない
} else if (dot > lengthSqCapsule) {
  // ジョイントがカプセルの終点側にある場合
    delta -= offsetToTail;
} else {
    // ジョイントがカプセルの始点と終点の間にある場合
    delta -= offsetToTail * (dot / offsetToTail.sqMagnitude);
}

// ジョイントとコライダーの距離。負の値は衝突していることを示す
var distance = collider.radius - jointRadius - delta.magnitude;

// ジョイントとコライダーの距離の方向。衝突している場合、この方向にジョイントを押し出す
var direction = -delta.normalized;
```

> Implementation Note: 内部コライダーでない通常のカプセルコライダーとの実装の差異は、距離の計算および方向の計算の部分のみです。他の箇所について、通常のカプセルコライダーの実装を流用することができます。

### Plane Collider

以下は、擬似コードによる平面コライダーの参考実装です。

```ts
var transformedOffset = collider.offset * collider.worldMatrix;
var transformedNormal = (colliderNormal * normalMatrixFrom(collider.worldMatrix)).normalized;
var delta = nextTail - transformedOffset;

// ジョイントとコライダーの距離。負の値は衝突していることを示す
var distance = dot(delta, transformedNormal) - jointRadius;

// ジョイントとコライダーの距離の方向。衝突している場合、この方向にジョイントを押し出す
var direction = transformedNormal;
```
