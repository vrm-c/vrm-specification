# VRMC_node_constraint

*Version 1.0-beta*

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [Overview](#overview)
  - [Purposes](#purposes)
- [Constraints](#constraints)
  - [Sources](#sources)
  - [Weight](#weight)
  - [Roll Constraint](#roll-constraint)
    - [Purposes](#purposes-1)
    - [Hierarchy](#hierarchy)
    - [Roll Axis](#roll-axis)
    - [Evaluation of rotations](#evaluation-of-rotations)
    - [Example of Implementation](#example-of-implementation)
  - [Aim Constraint](#aim-constraint)
    - [Purposes](#purposes-2)
    - [Hierarchy](#hierarchy-1)
    - [Aim Axis](#aim-axis)
    - [Evaluation of rotations](#evaluation-of-rotations-1)
    - [Example of Implementation](#example-of-implementation-1)
  - [Rotation Constraint](#rotation-constraint)
    - [Purposes](#purposes-3)
    - [Evaluation of rotations](#evaluation-of-rotations-2)
    - [Example of Implementation](#example-of-implementation-2)
- [glTF Schema Updates](#gltf-schema-updates)
  - [Extending Nodes](#extending-nodes)
  - [VRMC_node_constraint](#vrmc_node_constraint)
    - [Properties](#properties)
    - [VRMC_node_constraint.specVersion ✅](#vrmc_node_constraintspecversion-)
    - [VRMC_node_constraint.constraint ✅](#vrmc_node_constraintconstraint-)
  - [constraint](#constraint)
    - [Properties](#properties-1)
    - [constraint.roll](#constraintroll)
    - [constraint.aim](#constraintaim)
    - [constraint.rotation](#constraintrotation)
  - [rollConstraint](#rollconstraint)
    - [Properties](#properties-2)
    - [rollConstraint.source ✅](#rollconstraintsource-)
    - [rollConstraint.rollAxis ✅](#rollconstraintrollaxis-)
    - [rollConstraint.weight](#rollconstraintweight)
  - [aimConstraint](#aimconstraint)
    - [Properties](#properties-3)
    - [aimConstraint.source ✅](#aimconstraintsource-)
    - [aimConstraint.aimAxis ✅](#aimconstraintaimaxis-)
    - [aimConstraint.weight](#aimconstraintweight)
  - [rotationConstraint](#rotationconstraint)
    - [Properties](#properties-4)
    - [rotationConstraint.source ✅](#rotationconstraintsource-)
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

この拡張は、glTFシーン内のあるNodeのTransformを他のNodeによって制約することを可能とします。

この拡張では、 Roll Constraint, Aim Constraint, Rotation Constraint を定義しています。

### Purposes

VRMで扱うコンストレイントは、リアルタイムに[Humanoidボーン](../VRMC_vrm-1.0-beta//humanoid.ja.md)に対して割り当てられる回転情報を用いて、補助ボーンを制御するようなユースケースを意識して設計されています。

また、プラットフォームを跨いでコンストレイントが用いられることを念頭に、用途に特化したセマンティックな定義を行います。
そのため、実装にはアーティストが本来実現したい結果が得られることを期待し、挙動が実装間で完全に一致することを目指すものではありません。

## Constraints

本拡張では、3つのコンストレイント *Roll Constraint*, *Aim Constraint*, *Rotation Constraint* が定義されています。

### Sources

各Constraintは、制約される*Destination*ノードと、それを制約する*Source*ノードをそれぞれ一つ指定します。

NodeがConstraintのSourceとなるためには、以下の条件が必要です:

- Sourceは、Destinationノードそれ自身ではない
- モデル空間（VRMのコア仕様で定義されています）で計算されるSourceは、ヒエラルキー上Destinationノードの子ノードではない
- Sourceは、他のコンストレイントと組み合わせ循環依存関係を作ってはならない

### Weight

各Constraintは、回転量をどの程度Destinationに伝えるかを表す*Weight*を指定します。

Weightは[0.0 - 1.0]の数値で表され、Destinationのレスト回転からConstraintによって決定される回転へのSpherical Linear Interpolation (slerp)を行います。

### Roll Constraint

Roll Constraintは、Sourceの回転のうち、ある一軸の回転のみをDestinationに伝えるために用いるコンストレイントです。

#### Purposes

Roll Constraintは、以下のような用途で使われることを想定します:

- 腕・脚のツイストボーン

#### Hierarchy

Roll Constraintは、例えば以下のような構造で使われることを前提とします:

```markdown
- LowerArm
  - Hand
  - Twist1 (RollConstraint, Source is LowerArm)
  - Twist2 (RollConstraint, Source is LowerArm)
```

#### Roll Axis

Roll Constraintは、*Roll Axis*を一軸指定することができ、それによりDestinationの回転のうちどの軸をSourceに伝えるかを指定します。

Roll Axisには、 `"X"` ・ `"Y"` ・ `"Z"` のいずれかを指定します。

#### Evaluation of rotations

Sourceの回転の評価は、Sourceのレスト状態を基準に、Destinationのレスト状態におけるRoll Axisまわりの回転を評価することが推奨されます。
また、SourceがRoll Axisまわり以外に回転している場合は、Roll Axisがその回転と同じ方向に向く最小の回転との差分を用いてロールの回転の評価を行うことが推奨されます。

#### Example of Implementation

> *このセクションはNon-Normativeです。*

以下に、擬似コードでの実装例を示します:

```js
deltaSrcQuat = srcRestQuat.inverse * srcQuat
deltaSrcQuatInParent = srcRestQuat * deltaSrcQuat * srcRestQuat.inverse // source to parent
deltaSrcQuatInDst = dstRestQuat.inverse * deltaSrcQuatInParent * dstRestQuat // parent to destination

toVec = rollAxis.applyQuaternion( deltaSrcQuatInDst )
fromToQuat = Quaternion.fromToRotation( rollAxis, toVec )

targetQuat = Quaternion.slerp(
  dstRestQuat,
  dstRestQuat * fromToQuat.inverse * deltaSrcQuatInDst,
  weight
)
```

### Aim Constraint

Aim Constraintは、DestinationがSourceの向きを向くような回転をさせるために用いるコンストレイントです。

#### Purposes

Aim Constraintは、以下のような用途で使われることを想定します:

- 衣服の袖

#### Hierarchy

Aim Constraintは、例えば以下のような構造で使われることを前提とします:

```markdown
- UpperArm
  - LowerArm
- Aim (AimConstraint, Source is LowerArm)
```

#### Aim Axis

Aim Constraintは、*Aim Axis*を一方向指定することができ、それによりDestinationのどの軸がSourceの方向を向くようにするかを指定します。

Aim Axisには、 `"PositiveX"` ・ `"NegativeX"` ・ `"PositiveY"` ・ `"NegativeY"` ・ `"PositiveZ"` ・ `"NegativeZ"` のいずれかを指定します。

#### Evaluation of rotations

Destinationの回転は、Destinationがレスト状態から、DestinationのAim Axisがワールド空間においてDestinationからSourceに向かって伸びるベクトルの方向を向くような最小の回転とすることが推奨されます。

#### Example of Implementation

> *このセクションはNon-Normativeです。*

以下に、擬似コードでの実装例を示します:

```js
fromVec = aimAxis.applyQuaternion( dstParentWorldQuat * dstRestQuat )
toVec = ( srcWorldPos - dstWorldPos ).normalized
fromToQuat = Quaternion.fromToRotation( fromVec, toVec )

targetQuat = Quaternion.slerp(
  dstRestQuat,
  dstParentWorldQuat.inverse * fromToQuat * dstParentWorldQuat * dstRestQuat,
  weight
)
```

### Rotation Constraint

Rotation Constraintは、Sourceの回転をDestinationの回転に移すためのコンストレイントです。

本拡張で定義されるRotation Constraintは、Local-Localとなります。

#### Purposes

Rotation Constraintは、以下のような用途で使われることを想定します:

- サブアーム

#### Evaluation of rotations

Sourceの回転は、Sourceがレスト状態からSourceのオリエンテーションでどのようにローカルで回転したかを観測し、それをDestinationのレスト状態を基準としてDestinationのオリエンテーションでローカルで回転させることが推奨されます。

> BlenderのBone ConstraintにおけるLocal-Local Copy Rotationと同じ挙動が期待されます。

#### Example of Implementation

> *このセクションはNon-Normativeです。*

以下に、擬似コードでの実装例を示します:

```js
srcDeltaQuat = srcRestQuat.inverse * srcQuat

targetQuat = Quaternion.slerp(
  dstRestQuat,
  dstRestQuat * srcDeltaQuat,
  weight
)
```

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
          "specVersion": "1.0-beta",
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

### VRMC_node_constraint

本拡張のルートオブジェクトです。

#### Properties

|               | 型       | 説明                    | 必須  |
|:--------------|:---------|:-----------------------|:------|
| `specVersion` | `string` | 本拡張の仕様バージョンを表します。 | ✅ Yes |
| `constraint`  | `object` | Constraintを表すオブジェクトです。 | ✅ Yes |

- JSON schema: [VRMC_node_constraint.schema.json](./schema/VRMC_node_constraint.schema.json)

#### VRMC_node_constraint.specVersion ✅

VRMC_node_constraint 拡張の仕様バージョンを表します。
値は `"1.0-beta"` です。

- 型: `string`
- 必須: Yes

#### VRMC_node_constraint.constraint ✅

[Constraint](#constraint) を表すオブジェクトです。

- 型: `object`
- 必須: Yes

---

### constraint

コンストレイントを含むオブジェクトです。

`roll` ・ `aim` ・ `rotation` のうち、いずれか一つのみを含む必要があります。

#### Properties

|            | 型       | 説明                         | 必須 |
|:-----------|:---------|:---------------------------|:-----|
| `roll`     | `object` | Roll Constraintを記述します。     | No   |
| `aim`      | `object` | Aim Constraintを記述します。      | No   |
| `rotation` | `object` | Rotation Constraintを記述します。 | No   |

- JSON schema: [VRMC_node_constraint.constraint.schema.json](./schema/VRMC_node_constraint.constraint.schema.json)

#### constraint.roll

[Roll Constraint](#rollConstraint) を記述します。

- 型: `object`
- 必須: No

#### constraint.aim

[Aim Constraint](#aimConstraint) を記述します。

- 型: `object`
- 必須: No

#### constraint.rotation

[Rotation Constraint](#rotationConstraint) を記述します。

- 型: `object`
- 必須: No

---

### rollConstraint

[Roll Constraint](#roll-constraint)を記述するオブジェクトです。

#### Properties

|            | 型        | 説明                    | 必須              |
|:-----------|:----------|:------------------------|:------------------|
| `source`   | `integer` | このNodeを制約するNodeのIndex | ✅ Yes             |
| `rollAxis` | `string`  | このConstraintのRoll Axis  | ✅ Yes             |
| `weight`   | `number`  | このConstraintのWeight     | No, 初期値: `1.0` |

- JSON schema: [VRMC_node_constraint.rollConstraint.schema.json](./schema/VRMC_node_constraint.rollConstraint.schema.json)

#### rollConstraint.source ✅

このNodeを制約するNodeのIndexを指定します。

- 型: `integer`
- 必須: Yes
- 最小値: `>= 0`

#### rollConstraint.rollAxis ✅

このConstraintのRoll Axisを指定します。

- 型: `string`
- 必須: Yes
- 許可された値:
  - `X`
  - `Y`
  - `Z`

#### rollConstraint.weight

このConstraintのWeightを指定します。

- 型: `number`
- 必須: No, 初期値: `1.0`

---

### aimConstraint

[Aim Constraint](#aim-constraint)を記述するオブジェクトです。

#### Properties

|           | 型        | 説明                    | 必須              |
|:----------|:----------|:------------------------|:------------------|
| `source`  | `integer` | このNodeを制約するNodeのIndex | ✅ Yes             |
| `aimAxis` | `string`  | このConstraintのAim Axis   | ✅ Yes             |
| `weight`  | `number`  | このConstraintのWeight     | No, 初期値: `1.0` |

- JSON schema: [VRMC_node_constraint.aimConstraint.schema.json](./schema/VRMC_node_constraint.aimConstraint.schema.json)

#### aimConstraint.source ✅

このNodeを制約するNodeのIndexを指定します。

- 型: `integer`
- 必須: Yes
- 最小値: `>= 0`

#### aimConstraint.aimAxis ✅

このConstraintのAim Axisを指定します。

- 型: `string`
- 必須: Yes
- 許可された値:
  - `PositiveX`
  - `NegativeX`
  - `PositiveY`
  - `NegativeY`
  - `PositiveZ`
  - `NegativeZ`

#### aimConstraint.weight

このConstraintのWeightを指定します。

- 型: `number`
- 必須: No, 初期値: `1.0`

---

### rotationConstraint

[Rotation Constraint](#rotation-constraint)を記述するオブジェクトです。

#### Properties

|          | 型        | 説明                    | 必須              |
|:---------|:----------|:------------------------|:------------------|
| `source` | `integer` | このNodeを制約するNodeのIndex | ✅ Yes             |
| `weight` | `number`  | このConstraintのWeight     | No, 初期値: `1.0` |

- JSON schema: [VRMC_node_constraint.rotationConstraint.schema.json](./schema/VRMC_node_constraint.rotationConstraint.schema.json)

#### rotationConstraint.source ✅

このNodeを制約するNodeのIndexを指定します。

- 型: `integer`
- 必須: Yes
- 最小値: `>= 0`

#### rotationConstraint.weight

このConstraintのWeightを指定します。

- 型: `number`
- 必須: No, 初期値: `1.0`

---

## Implementation Notes

> *このセクションはnon-normativeです。*

### Dependency resolution between constraints

constraintは、他のconstraintに依存することがあります。
constraintの処理中に、まだ更新されていないtransformを参照することを防ぐため、constraintsの更新は適切な順序で行われるべきです。

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
