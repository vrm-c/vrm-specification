# `VRMC_vrm.firstPerson`

本文書では、 `VRMC_vrm` 拡張のうち `firstPerson` フィールドについての仕様を示します。

## MeshAnnotation

Mesh単位での描画を制御します。

`extensions.VRMC_vrm.firstPerson.meshAnnotations[*]`

| 名前            | 備考                         |
|:----------------|:---------------------------|
| node            | 対象node(meshを持っている)へのindex |
| firstPersonFlag | 後述                         |

firstPersonFlag。VRアプリでモデルを使用した場合に、自モデルの描画をHMDとそれ以外で切り分けます。

## MeshAnnotation(enum)

`thirdPersonOnly` は、VR描画時に自分の頭部を非表示にすることを意図しています。VR視点カメラは、頭部の中に位置することが想定されます。そのため、顔・頭・髪のポリゴンが視界に見えてしまうのを防ぎます。

特に描き分ける必要ないオブジェクトは、 `both` にして、すべてのカメラから描画されるようにします。

`firstPersonOnly` は、自分だけに見えて他人視点からは見えない設定です。アプリ側で特別な用途が無ければ使いません。

`auto` はMeshを頭部(`thirdPersonOnly`)とその他(`both`)に分割してください。次の節で詳述します。

| 名前            | 1人称カメラ(VR視点) | その他のカメラ | 例                                    |
|:----------------|:-----------------|:---------|---------------------------------------|
| thirdPersonOnly | 描画しない          | 描画する   | 顔・目・頭・髪、帽子・ヘルメットなどVR視界を遮るもの  |
| firstPersonOnly | 描画する           | 描画しない  | プレイヤーだけに見えてたの視点からは見えないユーザーインタフェース |
| both            | 描画する           | 描画する   |                                       |
| auto            |                  |          | 後述                                  |

### MeshAnnotation.Autoのアルゴリズム

* Meshの頂点をすべて調べて、Headボーンとその子孫のボーンに対するWeightを持つ頂点を集める
* 上記の頂点を含む三角形と含まない三角形に２分したMeshを作成する
* 上記の頂点を含むMeshをThirdPersonOnly、含まないMeshをBothとしたに設定する

### MeshAnnotationが指定されていない場合

MeshAnnotationが指定されていないメッシュが存在する場合において、FirstPersonの機能を利用する場合、
そのメッシュには `auto` が設定されているとみなして処理を行ってください。

`firstPerson` プロパティそれ自体がVRM拡張に存在しなかった場合においても、
すべてのメッシュに `auto` が設定されているとみなして処理を行ってください。
