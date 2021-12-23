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
    - [Freeze Axes](#freeze-axes)
    - [Weight](#weight)
- [glTF Schema Updates](#gltf-schema-updates)
  - [Extending Nodes](#extending-nodes)
  - [constraints](#constraints)
    - [Properties](#properties)
    - [constraints.specVersion ✅](#constraintsspecversion-)
    - [constraints.rotation](#constraintsrotation)
  - [rotationConstraint](#rotationconstraint)
    - [Properties](#properties-1)
    - [rotationConstraint.source ✅](#rotationconstraintsource-)
    - [rotationConstraint.sourceSpace](#rotationconstraintsourcespace)
    - [rotationConstraint.destinationSpace](#rotationconstraintdestinationspace)
    - [rotationConstraint.freezeAxes](#rotationconstraintfreezeaxes)
    - [rotationConstraint.weight](#rotationconstraintweight)
- [Implementation Notes](#implementation-notes)
  - [Dependency resolution between constraints](#dependency-resolution-between-constraints)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

* 進藤哲郎
* 蘇柏彰
* 小渕豊

## Status

* Work in progress

## Dependencies

glTF 2.0の仕様に対して書かれています。

## Overview

この拡張は、glTFシーン内のあるnodeのtransformを他のnodeによって制約することを可能とします。

この拡張では、 Rotation Constraint を定義しています。

## Constraints

**Rotation Constraint** が定義されています。

### Sources

各constraintは、destinationノードを制約するsourceとなるノードを一つ指定します。

nodeがconstraintのsourceとなるためには、以下の条件が必要です:

- Sourceは、destinationノードそれ自身ではない
- Model Space（以下で記述）で計算されるSourceは、ヒエラルキー上destinationノードの子ノードではない
- Sourceは、他のコンストレイントと組み合わせ循環依存関係を作ってはならない

### Constraint spaces

各constraintは、オブジェクトスペースを2つ指定します: **source space** と **destination space** 。
Source spaceは、source nodeのtransformをどう観測するかを指定します。
Destination spaceは、destination nodeに対してどうtransformを適用するかを指定します。

オブジェクトスペースは、 **local space** もしくは **model space** となります。
スペースがlocal spaceの場合、transformはnodeのローカルスペースで評価されます。
スペースがmodel spaceの場合、transformはglTFのシーンのルートから相対的に評価されます。
ワールドスペースでtransformを評価することはできません。

### Rotation Constraint

Rotation Constraintは、nodeの回転を別のノードで制約します。

Source nodeとdestination nodeの回転は各々の初期状態から相対的に評価されます。すなわち、コンストレイントを適用しても、sourceの回転を動かさなければdestinationの回転は変化しません。

> **TODO**: 回転差分についてより詳細な説明が必要

#### Freeze Axes

Freeze axesが指定されている場合、各フリーズされた軸上で回転が制約されます。
軸がフリーズされていなければ、コンストレイントはその軸周りの回転に対して影響を及ぼしません。

> **TODO**: 軸のフリーズがどう実装されるか、説明が必要

#### Weight

Weightが指定されている場合、constraintによって及ぼされる回転は、単位クォータニオンから回転差分へのtをweightとした球面線形補間によって決定されます。

---

## glTF Schema Updates

### Extending Nodes

コンストレイントは、nodeに `VRMC_node_constraint` 拡張を追加することで記述されます。
以下は、 `NodeB` を `NodeA` で制約するRotation Constraintの記述例です:

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
  ],
  // 通常のGLTF-2.0の情報
  "materials": [
    {
      // ...
    }
  ]
}
```

---

### constraints

本拡張のルートオブジェクトです。

#### Properties

|               | 型       | 説明                         | 必須  |
|:--------------|:---------|:---------------------------|:------|
| `specVersion` | `string` | 本拡張の仕様バージョンを表します。      | ✅ Yes |
| `rotation`    | `object` | Rotation Constraintを記述します。 | ✅ Yes |

- JSON schema: [VRMC_node_constraint.schema.json](./schema/VRMC_node_constraint.schema.json)

#### constraints.specVersion ✅

VRMC_node_constraint 拡張の仕様バージョンを表します。
値は `"1.0-draft"` です。

- 型: `string`
- 必須: Yes

#### constraints.rotation

[Rotation Constraint](#rotationConstraint) を記述します。

- 型: `object`
- 必須: No

---

### rotationConstraint

A set of parameters of a rotation constraint can be used to constrain a rotation of a node by another node.

#### Properties

|                    | 型           | 説明                              | 必須                             |
|:-------------------|:-------------|:----------------------------------|:---------------------------------|
| `source`           | `integer`    | このnodeを制約するnodeのindex           | ✅ Yes                            |
| `sourceSpace`      | `string`     | Source nodeを評価するオブジェクトスペース      | No, 初期値: `model`              |
| `destinationSpace` | `string`     | Destination nodeを評価するオブジェクトスペース | No, 初期値: `model`              |
| `freezeAxes`       | `boolean[3]` | このconstraintによって制約される軸。X-Y-Z   | No, 初期値: `[true, true, true]` |
| `weight`           | `number`     | このconstraintのweight               | No, 初期値: `1.0`                |

- JSON schema: [VRMC_node_constraint.rotationConstraint.schema.json](./schema/VRMC_node_constraint.rotationConstraint.schema.json)

#### rotationConstraint.source ✅

このnodeを制約するnodeのindexを指定します。

- 型: `integer`
- 必須: Yes
- 最小値: `>= 0`

#### rotationConstraint.sourceSpace

Source nodeはこのオブジェクトスペースで評価されます。

- 型: `string`
- 必須: No, 初期値: `model`
- 許可された値:
  - `local`
  - `model`

#### rotationConstraint.destinationSpace

Destination nodeはこのオブジェクトスペースで評価されます。

- 型: `string`
- 必須: No, 初期値: `model`
- 許可された値:
  - `local`
  - `model`

#### rotationConstraint.freezeAxes

このconstraintによって制約される軸を指定します。X-Y-Zの順番です。

- 型: `boolean[3]`
- 必須: No, 初期値: `[true, true, true]`

#### rotationConstraint.weight

このconstraintのweightを指定します。

- 型: `number`
- 必須: No, 初期値: `1.0`

## Implementation Notes

*このセクションはnon-normativeです。*

### Dependency resolution between constraints

constraintは、他のconstraintに依存することがあります。
constraintの処理中に、まだ更新されていないtransformを参照することを防ぐため、constraintsの更新は適切な順序で行われるべきです。

constraintは以下のようなnodeに依存することがあります:

- source spaceがmodel spaceの場合、sourceの（モデルのrootまでの）祖先となるnode
- source node
- destination spaceがmodel spaceの場合、destinationの（モデルのrootまでの）祖先となるnode

以下の擬似コードは、constraintsがどのように更新されるべきか、手続きの一例を表したものです:

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
