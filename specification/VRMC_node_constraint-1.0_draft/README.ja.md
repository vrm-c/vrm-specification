# VRMC_node_constraint

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

この拡張では、 Position Constraint・Rotation Constraint・Aim Constraint の3つのconstraintを定義しています。

## Constraints

**Position Constraint** ・ **Rotation Constraint** ・ **Aim Constraint** の3種類が定義されています。

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

### Position Constraint

Position Constraintは、nodeの位置を別のノードで制約します。

Source nodeとdestination nodeの位置は各々の初期状態から相対的に評価されます。すなわち、コンストレイントを適用しても、sourceの位置を動かさなければdestinationの位置は変化しません。

#### Freeze Axes

Freeze axesが指定されている場合、各フリーズされた軸上で位置が制約されます。
軸がフリーズされていなければ、コンストレイントはその軸上の位置に対して影響を及ぼしません。

#### Weight

Weightが指定されている場合、constraintによって及ぼされる位置の差分は純粋にweightによって乗算されます。

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

### Aim Constraint

Aim Constraintは、nodeがある別のノードの方向を向くように回転させます。

回転差分は、 **aim vector** と **up vector** を用いて計算されます。計算手順は以下のとおりです:

- Destination nodeがAim Vectorの方向を向き、上向きをUp Vectorまわりの半球を向いていると仮定し、この状態を初期状態とする
- Destination nodeの上向きがUp Vectorまわりの半球を向いたまま、source nodeの向きを向くように回転させ、この状態を現状態とする
- 回転差分を、初期状態から現状態への回転とする

Destination nodeに適用される最終的な回転は、初期の回転差分から現在の回転差分への差分となります。すなわち、コンストレイントを適用しても、sourceの位置を動かさなければdestinationの回転は変化しません。

> **TODO**: 初期の回転差分を考慮すべきか？

#### Freeze Axes

Aim Constraintでは、freeze axesは2つの軸を制約します: Yaw と Pitch。

Yawがフリーズされている場合、destination nodeはup vector周りに回転します。
Pitchがフリーズされている場合、destination nodeはaim vectorとup vectorの外積周りに回転します。

#### Weight

Weightが指定されている場合、constraintによって及ぼされる回転は、単位クォータニオンから回転差分へのtをweightとした球面線形補間によって決定されます。

---

## glTF Schema Updates

### Extending Nodes

コンストレイントは、nodeに `VRMC_node_constraint-1.0` 拡張を追加することで記述されます。
以下は、 `NodeB` を `NodeA` で制約するPosition Constraintの記述例です:

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
          "constraint": {
            "position": {
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
      "extensions": {
        "VMRC_materials_mtoon": {}
      }
    }
  ]
}
```

---

### constraints

本拡張のルートオブジェクトです。

#### Properties

|            | 型       | 説明                              | 必須 |
|:-----------|:---------|:----------------------------------|:-----|
| `position` | `object` | Position Constraintを記述します。 | No   |
| `rotation` | `object` | Rotation Constraintを記述します。 | No   |
| `aim`      | `object` | Aim Constraintを記述します。      | No   |

このうち、どれか一つを記述してください。

- JSON schema: [VRMC_node_constraint.schema.json](./schema/VRMC_node_constraint.schema.json)

#### constraints.position

[Position Constraint](#positionConstraint) を記述します。

- 型: `object`
- 必須: No

#### constraints.rotation

[Rotation Constraint](#rotationConstraint) を記述します。

- 型: `object`
- 必須: No

#### constraints.aim

[Aim Constraint](#aimConstraint) を記述します。

- 型: `object`
- 必須: No

---

### positionConstraint

Position Constraintのパラメータ群です。nodeの位置を他のnodeで制約します。

#### Properties

|                    | 型           | 説明                                           | 必須                             |
|:-------------------|:-------------|:-----------------------------------------------|:---------------------------------|
| `source`           | `integer`    | このnodeを制約するnodeのindex                  | ✅ Yes                          |
| `sourceSpace`      | `string`     | Source nodeを評価するオブジェクトスペース      | No, 初期値: `model`              |
| `destinationSpace` | `string`     | Destination nodeを評価するオブジェクトスペース | No, 初期値: `model`              |
| `freezeAxes`       | `boolean[3]` | このconstraintによって制約される軸。X-Y-Z      | No, 初期値: `[true, true, true]` |
| `weight`           | `number`     | このconstraintのweight                         | No, 初期値: `1.0`                |

- JSON schema: [VRMC_node_constraint.positionConstraint.schema.json](./schema/VRMC_node_constraint.positionConstraint.schema.json)

#### positionConstraint.source ✅

このnodeを制約するnodeのindexを指定します。

- 型: `integer`
- 必須: Yes
- 最小値: `>= 0`

#### positionConstraint.sourceSpace

Source nodeはこのオブジェクトスペースで評価されます。

- 型: `string`
- 必須: No, 初期値: `model`
- 許可された値:
  - `local`
  - `model`

#### positionConstraint.destinationSpace

Destination nodeはこのオブジェクトスペースで評価されます。

- 型: `string`
- 必須: No, 初期値: `model`
- 許可された値:
  - `local`
  - `model`

#### positionConstraint.freezeAxes

このconstraintによって制約される軸を指定します。X-Y-Zの順番です。

- 型: `boolean[3]`
- 必須: No, 初期値: `[true, true, true]`

#### positionConstraint.weight

このconstraintのweightを指定します。

- 型: `number`
- 必須: No, 初期値: `1.0`

---

### rotationConstraint

A set of parameters of a rotation constraint can be used to constrain a rotation of a node by another node.

#### Properties

|                    | 型           | 説明                                           | 必須                             |
|:-------------------|:-------------|:-----------------------------------------------|:---------------------------------|
| `source`           | `integer`    | このnodeを制約するnodeのindex                  | ✅ Yes                          |
| `sourceSpace`      | `string`     | Source nodeを評価するオブジェクトスペース      | No, 初期値: `model`              |
| `destinationSpace` | `string`     | Destination nodeを評価するオブジェクトスペース | No, 初期値: `model`              |
| `freezeAxes`       | `boolean[3]` | このconstraintによって制約される軸。X-Y-Z      | No, 初期値: `[true, true, true]` |
| `weight`           | `number`     | このconstraintのweight                         | No, 初期値: `1.0`                |

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

---

### aimConstraint

A set of parameters of an aim constraint can be used to rotate a node to make it look toward another node.

#### Properties

|                    | 型           | 説明                                           | 必須                       |
|:-------------------|:-------------|:-----------------------------------------------|:---------------------------|
| `source`           | `integer`    | このnodeを制約するnodeのindex                  | ✅ Yes                    |
| `sourceSpace`      | `string`     | Source nodeを評価するオブジェクトスペース      | No, 初期値: `model`        |
| `destinationSpace` | `string`     | Destination nodeを評価するオブジェクトスペース | No, 初期値: `model`        |
| `aimVector`        | `number[3]`  | このconstraintのaim vectorを指定します。       | No, 初期値: `[0, 0, 1]`    |
| `upVector`         | `number[3]`  | このconstraintのup vectorを指定します。        | No, 初期値: `[0, 1, 0]`    |
| `freezeAxes`       | `boolean[2]` | このconstraintによって制約される軸。Yaw-Pitch  | No, 初期値: `[true, true]` |
| `weight`           | `number`     | このconstraintのweight                         | No, 初期値: `1.0`          |

- JSON schema: [VRMC_node_constraint.aimConstraint.schema.json](./schema/VRMC_node_constraint.aimConstraint.schema.json)

#### aimConstraint.source ✅

このnodeを制約するnodeのindexを指定します。

- 型: `integer`
- 必須: Yes
- 最小値: `>= 0`

#### aimConstraint.sourceSpace

Source nodeはこのオブジェクトスペースで評価されます。

- 型: `string`
- 必須: No, 初期値: `model`
- 許可された値:
  - `local`
  - `model`

#### aimConstraint.destinationSpace

Destination nodeはこのオブジェクトスペースで評価されます。

- 型: `string`
- 必須: No, 初期値: `model`
- 許可された値:
  - `local`
  - `model`

#### aimConstraint.aimVector

このconstraintのaim vectorを指定します。

- 型: `number[3]`
- 必須: No, 初期値: `[0, 0, 1]`

#### aimConstraint.upVector

このconstraintのup vectorを指定します。

- 型: `number[3]`
- 必須: No, 初期値: `[0, 1, 0]`

#### aimConstraint.freezeAxes

このconstraintによって制約される軸を指定します。Yaw-Pitchの順番です。

- 型: `boolean[2]`
- 必須: No, 初期値: `[true, true]`

#### aimConstraint.weight

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
