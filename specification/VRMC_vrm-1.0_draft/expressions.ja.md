# `VRMC_vrm.expressions`

本文書では、 `VRMC_vrm` 拡張のうち `expressions` フィールドについての仕様を示します。

Expression は、

* MorphTarget
* MaterialColor
* TextureTransform

のグループに対して意味を指定する機能です。

> 例えば、 `口をへの字にする MorphTarget` と `目を閉じる MorphTarget` の組み合わせを `sad` にするなど

## Expressionの仕様

| 名前                                 | 備考                                                                               |
|:-------------------------------------|:-----------------------------------------------------------------------------------|
| expressions[*].preset                | 上記のExpressionを一意に識別するために以下の制約に従ってください                   |
| expressions[*].name                  | 上記のExpressionを一意に識別するために以下の制約に従ってください                   |
| expressions[*].isBinary              | trueの場合 value!=0 を 1 とみなします                                              |
| expressions[*].morphTargetBinds      | MorphTargetBind(後述) のリスト                                                     |
| expressions[*].materialColorBinds    | MaterialValueBind(後述) のリスト                                                   |
| expressions[*].textureTransformBinds | TextureTransformBind(後述) のリスト                                                |
| expressions[*].overrideMouth         | このExpressionのWeightが0でないときに、リップシンク(後述) のウェイトを操作します。 |
| expressions[*].overrideBlink         | このExpressionのWeightが0でないときに、瞬き(後述) のウェイトを操作します。         |
| expressions[*].overrideLookAt        | このExpressionのWeightが0でないときに、視線(後述) のウェイトを操作します。         |

### Expression の識別

各Expressionを一意に識別するために以下の制約に従ってください

preset が custom 以外の時

* preset を重複させない
* name を 空文字列にする

preset が custom の時

* preset custom は重複してよい
* name を重複させない

### Expression の制御

各Expressionが用いられる際に、その表情の強さを表す「Value」の状態を持つことを想定します。
Valueは [0-1] の範囲の値を持つ数値です。
VRMの実装は、アプリケーションがこの範囲から外れた値を与えた場合は、値をクランプしてください。

## ExpressionPreset(enum)

### ユーザー定義(enum)

| 名前   | 備考                                                                                    |
|:-------|:----------------------------------------------------------------------------------------|
| custom | Applicationが独自に決めたExpressionを使う場合などに使用します。nameで識別してください。 |

### 感情(enum)

| 名前      | 備考                   |
|:----------|:-----------------------|
| happy     | 喜。 `joy` から変更    |
| angry     | 怒                     |
| sad       | 哀。 `sorrow` から変更 |
| relaxed   | 楽。 `fun` から変更    |
| surprised | 驚。 `1.0で新規追加`   |

特に具体的な顔の変形について仕様を規定していません。

### リップシンク(enum)

| 名前 | 備考 |
|:-----|:-----|
| aa   | あ   |
| ih   | い   |
| ou   | う   |
| ee   | え   |
| oh   | お   |

### 瞬き(enum)

| 名前       | 備考             |
|:-----------|:-----------------|
| blink      | 両方の瞼を閉じる |
| blinkLeft  | 左瞼を閉じる     |
| blinkRight | 右瞼を閉じる     |

### 視線(enum)

| 名前      | 備考                                                                         |
|:----------|:-----------------------------------------------------------------------------|
| lookUp    | ボーンではなくExpressionで視線が動くモデル向け。視線制御を参照してください。 |
| lookDown  | ボーンではなくExpressionで視線が動くモデル向け。視線制御を参照してください。 |
| lookLeft  | ボーンではなくExpressionで視線が動くモデル向け。視線制御を参照してください。 |
| lookRight | ボーンではなくExpressionで視線が動くモデル向け。視線制御を参照してください。 |

### その他(enum)

| 名前    | 備考                             |
|:--------|:---------------------------------|
| neutral | 後方互換性のために残しています。 |

### overrideMouth, overrideBlink, overrideLookAt

リップシンク、瞬き、視線 はシステムにより自動で値が生成されることが想定されます。
そのため、 これらの Expression が他の Expression と同時に有効になってしまい、
メッシュが破綻してしまう可能性があります。

例えば、

* `happy` 中に `aa` が来て口が2倍開いてしまう
* `sad` 中に `blink` が来て目が2回閉じてしまう
* `blink` 中に `lookAt` が来て目がはみ出す

などです。
これらを防御するために、 Expression に対して overrideMouth, overrideBlink, overrideLookAt を設定できます。

それぞれの override プロパティは、以下のプリセット表情に対して作用します:

| プロパティ            | 作用する表情                                    |
|:-----------------|:----------------------------------------------|
| `overrideMouth`  | `aa`, `ih`, `ou`, `ee`, `oh`                  |
| `overrideBlink`  | `blink`, `blinkLeft`, `blinkRight`            |
| `overrideLookAt` | `lookUp`, `lookDown`, `lookLeft`, `lookRight` |

> カスタム表情に対してこれらの override プロパティが作用するかは、仕様では特に定義しません。
> アプリケーション側の需要に応じて適宜設定を行ってください。
> 例えば、VRMのプリセットに含まれないリップシンクをカスタム表情を用いてアプリケーションごとに利用したい場合に、そのカスタム表情の発現を override プロパティを用いて制御するような使い方が想定されます。
>
> 上記の理由で、VRMの実装は、カスタム表情を override の対象に含むことができるようなインタフェースを提供することが推奨されます。

blink に対する overrideBlink のように、同種同士に対する設定は無効として扱います。

設定内容はすべて同じで、効果は下記のとおりです。

| 名前  | 備考                                                                                                                                                                           |
|:------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| none  | 何もしない                                                                                                                                                                     |
| block | 対象の weight を 0 にする。例えば、 happy に overrideBlink=block が設定されているときに、 happy.weight>0 になると blink,blinkLeft,blinkRight の weight を 0 に override する。 |
| blend | 対象の weight を 減衰させる。例えば、 happy に overrideBlink=blend が設定されているときに、 blink,blinkLeft,blinkRight を happy.weight とブレンドして減衰させる(後述)。        |

blend 詳細

例えば、happy が overrideBlink = blend に設定されている場合、
happy の値が 0 から 1 にフェードするに従い、線形に blink を減衰させます。
0~1 の間の中間値の挙動が block と異なります。

```js
// 何らか疑似コードで示す。仮
var value = 0;
if (happyWeight > 0 && happy.overrideBlink == "blend") value += happyWeight;
if (angryWeight > 0 && happy.overrideBlink == "blend") value += angryWeight;
if (sadWeight > 0 && happy.overrideBlink == "blend") value += sadWeight;
if (relaxedWeight > 0 && happy.overrideBlink == "blend") value += relaxedWeight;
if (surprisedWeight > 0 && happy.overrideBlink == "blend") value += surprisedWeight;
var factor = 1.0 - saturate(value);
SetBlinkWeight(blinkWeight * factor);
```

### MorphTargetBind

`extensions.VRMC_vrm.expressions[*].morphTargetBinds[*]`

Expression と MorphTarget を結びつけます。

| 名前   | 備考                                                    |
|:-------|:------------------------------------------------------|
| node   | 対象node(meshを持っている)のindex                             |
| index  | 対象morphのindex(すべてのprimitiveが同じmorphTargetを持つ想定です) |
| weight | 適用したときのmorph値 [0-1]。0.X では [0-100]                   |

### MaterialColorBind

`extensions.VRMC_vrm.expressions[*].materialColorBinds[*]`

Expression と Material の色の変化を結びつけます。

| 名前        | 備考                                             |
|:------------|:-------------------------------------------------|
| material    | 対象のmaterialのindex                            |
| type        | materialの変更対象項目(color, uvScale, uvOffset) |
| targetValue | 適用したときのmaterial値(float4)                 |

`extensions.VRMC_vrm.expressions[*].materialColorBinds[*].type`

それぞれ、以下のパラメータに対応します:

| 名前          | `pbrMetallicRoughness`                 | `KHR_materials_unlit`                  | `VRMC_materials_mtoon`                          |
|:--------------|:---------------------------------------|:---------------------------------------|:------------------------------------------------|
| color         | `pbrMetallicRoughness.baseColorFactor` | `pbrMetallicRoughness.baseColorFactor` | `pbrMetallicRoughness.baseColorFactor`          |
| emissionColor | `emissiveFactor`                       | 未使用                                 | `emissiveFactor`                                |
| shadeColor    | 未使用                                 | 未使用                                 | `extensions.VRMC_materials_mtoon.shadeFactor`   |
| rimColor      | 未使用                                 | 未使用                                 | `extensions.VRMC_materials_mtoon.rimFactor`     |
| outlineColor  | 未使用                                 | 未使用                                 | `extensions.VRMC_materials_mtoon.outlineFactor` |

### TextureTransformBind

`extensions.VRMC_vrm.expressions[*].textureTransformBinds[*]`

Expression と 対象 Material のテクスチャーの scale, offset の変化を結びつけます。
対象マテリアルで使われるテクスチャのうち、UVアクセスするテクスチャすべてが同じ値を使用することとします。

UVアクセスしないテクスチャは、MToon の `matcap` です。

| 名前     | 備考                                          |
|:---------|:----------------------------------------------|
| material | 対象のmaterialのindex                         |
| scale    | 適用したときのscale値(float2, default=[1, 1]) |
| offset   | 適用したときのoffset値(float2)                |

## Expression 更新のアルゴリズム

### MorphTarget

* すべての MorphTarget が0の状態にする
* Expression の値(Weight)を積算する `void AccumulateValue(Expression expression, float value)`
* 積算された値を適用する

### MaterialColor と TextureTransform

* すべての MaterialColor と TextureTransofrm を初期状態にする(0ではなく)
* Expression の値(Weight)を積算する `void AccumulateValue(Exoressuib expression, float value)`
* 積算された値を適用する `Base + (A.Target - Base) * A.Weight + (B.Target - Base) * B.Weight`
  * MaterialColor と TextureTransform は初期値が 0 とは限らないので、初期値との差分を積算します。
