# VRM仕様

[glTF-2.0](https://github.com/KhronosGroup/glTF/blob/master/specification/2.0/README.md)のバイナリ形式glbをベースにした、VR向けモデルフォーマットです。
VRM仕様のリポジトリはこちら：[VRM specification](https://github.com/vrm-c/vrm-specification)。 

# 更新履歴

* 20181109: JsonShcemaの`Vector3`のタイプが誤って `array` になっていた部分を修正

```js
{
    "x": {
        "type": "number"
    },
    "y": {
        "type": "number"
    },
    "z": {
        "type": "number"
    }
}
```

# 目次

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [拡張子](#%E6%8B%A1%E5%BC%B5%E5%AD%90)
- [Json拡張](#json%E6%8B%A1%E5%BC%B5)
- [VRM拡張: VRMバージョンなど](#vrm%E6%8B%A1%E5%BC%B5-vrm%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3%E3%81%AA%E3%81%A9)
- [保存できる要素](#%E4%BF%9D%E5%AD%98%E3%81%A7%E3%81%8D%E3%82%8B%E8%A6%81%E7%B4%A0)
  - [GLTF-2.0: Texture](#gltf-20-texture)
  - [GLTF-2.0: Material(json.extensions.VRM.materialProperties)](#gltf-20-materialjsonextensionsvrmmaterialproperties)
  - [VRM拡張: `/extensions/VRM/materialProperties`](#vrm%E6%8B%A1%E5%BC%B5-extensionsvrmmaterialproperties)
  - [GLTF-2.0: Mesh](#gltf-20-mesh)
  - [GLTF-2.0: スキニング情報](#gltf-20-%E3%82%B9%E3%82%AD%E3%83%8B%E3%83%B3%E3%82%B0%E6%83%85%E5%A0%B1)
  - [GLTF-2.0: Node](#gltf-20-node)
- [保存する値に対する規約](#%E4%BF%9D%E5%AD%98%E3%81%99%E3%82%8B%E5%80%A4%E3%81%AB%E5%AF%BE%E3%81%99%E3%82%8B%E8%A6%8F%E7%B4%84)
  - [GLTF2の規約](#gltf2%E3%81%AE%E8%A6%8F%E7%B4%84)
  - [VRMの規約](#vrm%E3%81%AE%E8%A6%8F%E7%B4%84)
- [VRMが提供するシェーダー](#vrm%E3%81%8C%E6%8F%90%E4%BE%9B%E3%81%99%E3%82%8B%E3%82%B7%E3%82%A7%E3%83%BC%E3%83%80%E3%83%BC)
  - [Unlit系](#unlit%E7%B3%BB)
  - [MToon](#mtoon)
- [VRM拡張: モデルのボーンマッピング(json.extensions.VRM.humanoid)](#vrm%E6%8B%A1%E5%BC%B5-%E3%83%A2%E3%83%87%E3%83%AB%E3%81%AE%E3%83%9C%E3%83%BC%E3%83%B3%E3%83%9E%E3%83%83%E3%83%94%E3%83%B3%E3%82%B0jsonextensionsvrmhumanoid)
  - [定義しているボーン](#%E5%AE%9A%E7%BE%A9%E3%81%97%E3%81%A6%E3%81%84%E3%82%8B%E3%83%9C%E3%83%BC%E3%83%B3)
- [VRM拡張: モデル情報(json.extensions.VRM.meta)](#vrm%E6%8B%A1%E5%BC%B5-%E3%83%A2%E3%83%87%E3%83%AB%E6%83%85%E5%A0%B1jsonextensionsvrmmeta)
  - [情報](#%E6%83%85%E5%A0%B1)
  - [使用許諾・ライセンス情報](#%E4%BD%BF%E7%94%A8%E8%A8%B1%E8%AB%BE%E3%83%BB%E3%83%A9%E3%82%A4%E3%82%BB%E3%83%B3%E3%82%B9%E6%83%85%E5%A0%B1)
- [VRM拡張: モーフ設定(json.extensions.VRM.blendShapeMaster)](#vrm%E6%8B%A1%E5%BC%B5-%E3%83%A2%E3%83%BC%E3%83%95%E8%A8%AD%E5%AE%9Ajsonextensionsvrmblendshapemaster)
  - [ブレンドシェイプグループ(json.extensions.VRM.blendShapeMaster.blendShapeGroups)](#%E3%83%96%E3%83%AC%E3%83%B3%E3%83%89%E3%82%B7%E3%82%A7%E3%82%A4%E3%83%97%E3%82%B0%E3%83%AB%E3%83%BC%E3%83%97jsonextensionsvrmblendshapemasterblendshapegroups)
- [VRM拡張: 一人称設定(json.extensions.VRM.firstPerson)](#vrm%E6%8B%A1%E5%BC%B5-%E4%B8%80%E4%BA%BA%E7%A7%B0%E8%A8%AD%E5%AE%9Ajsonextensionsvrmfirstperson)
  - [firstPersonBone(json.extensions.VRM.firstPerson.firstPersonBone)](#firstpersonbonejsonextensionsvrmfirstpersonfirstpersonbone)
  - [firstPersonBoneOffset(json.extensions.VRM.firstPerson.firstPersonBoneOffset)](#firstpersonboneoffsetjsonextensionsvrmfirstpersonfirstpersonboneoffset)
  - [meshAnnotations(json.extensions.VRM.firstPerson.meshAnnotations)](#meshannotationsjsonextensionsvrmfirstpersonmeshannotations)
  - [視線設定](#%E8%A6%96%E7%B7%9A%E8%A8%AD%E5%AE%9A)
- [VRM拡張: 揺れモノ設定(json.extensions.VRM.secondaryAnimation)](#vrm%E6%8B%A1%E5%BC%B5-%E6%8F%BA%E3%82%8C%E3%83%A2%E3%83%8E%E8%A8%AD%E5%AE%9Ajsonextensionsvrmsecondaryanimation)
  - [揺れるボーン(secondaryAnimation.boneGroups)](#%E6%8F%BA%E3%82%8C%E3%82%8B%E3%83%9C%E3%83%BC%E3%83%B3secondaryanimationbonegroups)
  - [揺れモノ当たり判定設定(json.extensions.VRM.secondaryAnimation.colliderGroups)](#%E6%8F%BA%E3%82%8C%E3%83%A2%E3%83%8E%E5%BD%93%E3%81%9F%E3%82%8A%E5%88%A4%E5%AE%9A%E8%A8%AD%E5%AE%9Ajsonextensionsvrmsecondaryanimationcollidergroups)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->


# 拡張子

``.vrm``を使います。
gltfのバイナリ形式``.glb``と互換性があるので、拡張子を``.glb``に変更するとGLTF対応のアプリケーションで読み込むことができます(VRM独自の追加情報は無くなります)。

# Json拡張

GLBのJSON部に`VRM Extension`として拡張しています。

```js
{
  "extensionsUsed": {
    "VRM"
  },
  "extensions": {
    "VRM": {
      // VRMの拡張情報
    }
  },
  // 通常のGLTF-2.0の情報
}
```

仕様のJsonSchemaを作成しました

* https://github.com/vrm-c/vrm-specification/tree/master/specification/VRMC_vrm-1.0_draft/schema

GLTF-2.0のJsonSchema

* https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/schema

# VRM拡張: VRMバージョンなど

* `/extensions/VRM/exporterVersion`はv0.36から

```js
{
    "title": "vrm",
    "description": "VRM extension is for 3d humanoid avatars (and models) in VR applications.",
    "type": "object",
    "properties": {
        "exporterVersion": {
            "description": "Version of exporter that vrm created. UniVRM-0.42",
            "type": "string"
        },
        "meta": {
            "$ref": "vrm.meta.schema.json"
        },
        "humanoid": {
            "$ref": "vrm.humanoid.schema.json"
        },
        "firstPerson": {
            "$ref": "vrm.firstperson.schema.json"
        },
        "blendShapeMaster": {
            "$ref": "vrm.blendshape.schema.json"
        },
        "secondaryAnimation": {
            "$ref": "vrm.secondaryanimation.schema.json"
        },
        "materialProperties": {
            "type": "array",
            "items": {
                "$ref": "vrm.material.schema.json"
            }
        }
    }
}
```

# 保存できる要素

人間型モデル一体分の情報を保存します。

## GLTF-2.0: Texture

* GLTF-2.0の`/textures/`

VRM拡張はありません。

## GLTF-2.0: Material(json.extensions.VRM.materialProperties)

* GLTF-2.0の`/materials/`

GLTFのマテリアルにフォールバックした情報を保存しています(拡張子をGLBに変更した場合に使われる)。

## VRM拡張: `/extensions/VRM/materialProperties`

VRM独自のマテリアル情報を保存しています。
現状、Unityに必要な項目を保存しています。
選択可能なShaderは、[VRMが提供するシェーダー](#vrmshader)を参照してください。

* https://github.com/vrm-c/UniVRM/blob/master/specification/0.0/schema/vrm.material.schema.json

```js
{
    "title": "vrm.material",
    "type": "object",
    "properties": {
        "name": {
            "type": "string"
        },
        "shader": {
            "type": "string"
        },
        "renderQueue": {
            "type": "integer"
        },
        "floatProperties": {
            "type": "object"
        },
        "vectorProperties": {
            "type": "object"
        },
        "textureProperties": {
            "type": "object"
        },
        "keywordMap": {
            "type": "object"
        },
        "tagMap": {
            "type": "object"
        }
    }
}
```

## GLTF-2.0: Mesh

* GLTF-2.0の`/meshes/`

VRM拡張はありません。

### 頂点属性

* GLTF-2.0の`/meshes/*/primitives/*/attributes`

  * TANGENT (vec4) // v0.42から保存するのをやめて、import時にnormalとuvから計算するようにしています。

### モーフターゲット情報

* `/meshes/*/primitives/*/extras/targetNames`

にMoprhTargetの名称を記録しています。

## GLTF-2.0: スキニング情報

* GLTF-2.0の`/skins/`

VRM拡張はありません。

## GLTF-2.0: Node

* GLTF-2.0の`/nodes/`

VRM拡張はありません。

* node
  * name
  * position(vec3)
  * rotation(quaternion)
  * scale(vec3)

# 保存する値に対する規約

## GLTF2の規約

GLTF2の規約に準拠します。
特に重要な項目です。

* メートル単位
* 右手Y-UP座標系[^OpenGLCoord]

[^OpenGLCoord]: OpenGL座標系。+Xが右、+Yが上、+Zが手前です。

## VRMの規約

人間型モデルに特化して互換性を高めるために、以下の制約を課します。

* モデルは原点に位置する
* モデルは-Z向き[^OpenGLCoord]
* モデルのヒエラルキーはY-UP[^ZUP]
* モデルのメッシュはY-UP[^ZUP]
* モデルのヒエラルキーはT-Pose
* モデルのメッシュはT-Pose
* ボーンに回転を入れない
* ボーンにスケールを入れない
* ヘッドボーンは真正面を向いている[^LookAt]

[^ZUP]: Blenderや3ds MaxなどのZ-UPのモデラーに由来するモデルでヒエラルキーの途中でx軸-90度の回転を入れてZ-UPが入れ子になっている場合があります。
[^LookAt]: 視線制御はT-Pose時のヘッドの向きを基準に目標物の方向を計算します。

<a name="vrmshader"></a>

# VRMが提供するシェーダー

セル調のキャラクタモデルの運用を想定して以下のシェーダーを用意しています。

## Unlit系

ライティング・シェーディングせずにテクスチャ色をそのまま表示します。
半透明の扱い別に４種類を用意しています。

* UnlitTexture(不透明)
* UnlitCutout(透明度が閾値以下の部分を透明とする)
* UnlitTransparent(アルファブレンド。ZWriteしない)[^Transparent]
* UnlitTransparentZWrite(アルファブレンド。ZWriteする)[^TransparentZWrite]

[^Transparent]: 煙や頬の色など実体の無いオブジェクト向けです。
[^TransparentZWrite]: 半透明の衣装や、髪の先が半透明など実体のあるオブジェクト向けです。

## MToon

セルシェーディング、輪郭線に対応したシェーダー。
Unlitよりもきめ細かく設定できます。

# VRM拡張: モデルのボーンマッピング(json.extensions.VRM.humanoid)

NodeとHumanoidで定義される標準ボーンの対応表です。

```js
{
    "title": "vrm.humanoid.bone",
    "type": "object",
    "properties": {
        "bone": {
            "description": "Human bone name.",
            "type": "string",
            "enum": ["hips","leftUpperLeg","rightUpperLeg","leftLowerLeg","rightLowerLeg","leftFoot","rightFoot","spine","chest","neck","head","leftShoulder","rightShoulder","leftUpperArm","rightUpperArm","leftLowerArm","rightLowerArm","leftHand","rightHand","leftToes","rightToes","leftEye","rightEye","jaw","leftThumbProximal","leftThumbIntermediate","leftThumbDistal","leftIndexProximal","leftIndexIntermediate","leftIndexDistal","leftMiddleProximal","leftMiddleIntermediate","leftMiddleDistal","leftRingProximal","leftRingIntermediate","leftRingDistal","leftLittleProximal","leftLittleIntermediate","leftLittleDistal","rightThumbProximal","rightThumbIntermediate","rightThumbDistal","rightIndexProximal","rightIndexIntermediate","rightIndexDistal","rightMiddleProximal","rightMiddleIntermediate","rightMiddleDistal","rightRingProximal","rightRingIntermediate","rightRingDistal","rightLittleProximal","rightLittleIntermediate","rightLittleDistal","upperChest"]
        },
        "node": {
            "description": "Reference node index",
            "type": "integer"
        },
        "useDefaultValues": {
            "description": "Unity's HumanLimit.useDefaultValues",
            "type": "boolean"
        },
        "min": {
            "description": "Unity's HumanLimit.min",
            "type": "array"
        },
        "max": {
            "description": "Unity's HumanLimit.max",
            "type": "array"
        },
        "center": {
            "description": "Unity's HumanLimit.center",
            "type": "array"
        },
        "axisLength": {
            "description": "Unity's HumanLimit.axisLength",
            "type": "number"
        }
    }
}
```

## 定義しているボーン

|ボーン名前             |必須・オプション|
|:---------------------|:--------------|
|neck                  |必須      |
|head                  |必須           |
|left/right Eye        |オプション      |
|jaw                   |オプション      |
|hips                  |必須           |
|spine                 |必須           |
|chest                 |必須      |
|upperChest            |オプション      |
|left/right Shoulder   |オプション      |
|left/right UpperArm   |必須           |
|left/right LowerArm   |必須           |
|left/right Hand       |必須           |
|left/right UpperLeg   |必須           |
|left/right LowerLeg   |必須           |
|left/right Foot       |必須           |
|left/right Toe        |オプション      |  
|left/right Thumb Proximal, Intermediate, Distal |オプション|
|left/right Index Proximal, Intermediate, Distal |オプション|
|left/right Middle Proximal, Intermediate, Distal|オプション|
|left/right Ring Proximal, Intermediate, Distal  |オプション|
|left/right Little Proximal, Intermediate, Distal|オプション|

# VRM拡張: モデル情報(json.extensions.VRM.meta)

```js
{
    "title": "vrm.meta",
    "type": "object",
    "properties": {
        "title": {
            "description": "Title of VRM model",
            "type": "string"
        },
        "version": {
            "description": "Version of VRM model",
            "type": "string"
        },
        "author": {
            "description": "Author of VRM model",
            "type": "string"
        },
        "contactInformation": {
            "description": "Contact Information of VRM model author",
            "type": "string"
        },
        "reference": {
            "description": "Reference of VRM model",
            "type": "string"
        },
        "texture": {
            "description": "Thumbnail of VRM model",
            "type": "integer"
        },
        "allowedUserName": {
            "description": "A person who can perform with this avatar",
            "type": "string",
            "enum": ["OnlyAuthor","ExplicitlyLicensedPerson","Everyone"]
        },
        "violentUssageName": {
            "description": "Permission to perform violent acts with this avatar",
            "type": "string",
            "enum": ["Disallow","Allow"]
        },
        "sexualUssageName": {
            "description": "Permission to perform sexual acts with this avatar",
            "type": "string",
            "enum": ["Disallow","Allow"]
        },
        "commercialUssageName": {
            "description": "For commercial use",
            "type": "string",
            "enum": ["Disallow","Allow"]
        },
        "otherPermissionUrl": {
            "description": "If there are any conditions not mentioned above, put the URL link of the license document here.",
            "type": "string"
        },
        "licenseName": {
            "description": "License type",
            "type": "string",
            "enum": ["Redistribution_Prohibited","CC0","CC_BY","CC_BY_NC","CC_BY_SA","CC_BY_NC_SA","CC_BY_ND","CC_BY_NC_ND","Other"]
        },
        "otherLicenseUrl": {
            "description": "If “Other” is selected, put the URL link of the license document here.",
            "type": "string"
        }
    }
}
```

## 情報

### タイトル(Title)

アバターモデルの名前を設定します

### 作者(Author)

モデルの作者の名前を記述します

### 連絡先(Contact Information)

モデルの作者への連絡先を記述します

### 参照(Reference)

何か「親作品」に相当するものがある場合は参照URLなどを記述します

### サムネイル(Thumbnail)

アバターモデルのサムネイルを登録します。2048x2048程度の解像度テクスチャを推奨します。meta情報内ではtexture番号を指定します。

### バージョン

モデルの作成バージョンです。

## 使用許諾・ライセンス情報

License

### アバターの人格に関する許諾範囲

Personation / Charaterization Permission

#### アバターに人格を与えることの許諾範囲(json.extensions.VRM.meta.allowedUserName)

A person who can perform with this avatar

* アバターを操作することはアバター作者にのみ許される(Only Author)
* 明確に許可された人限定(Explictly Licensed Person)
* 全員に許可(Everyone)

#### このアバターを用いて暴力表現を演じることの許可(json.extensions.VRM.meta.violentUssageName)

Violent acts using this avatar

* 不許可(Disallow)
* 許可(Allow)

#### このアバターを用いて性的表現を演じることの許可(json.extensions.VRM.meta.sexualUssageName)

Sexuality acts using this avatar

* 不許可(Disallow)
* 許可(Allow)

#### 商用利用の許可(json.extensions.VRM.meta.commercialUssageName)

For commercial use

* 不許可(Disallow)
* 許可(Allow)

#### その他のライセンス条件(json.extensions.VRM.meta.otherPermissionUrl)

Other License Url

上記許諾条件以外のライセンス条件がある場合はそのライセンス文書へのURLを記述

### 再配布・改変に関する許諾範囲

Redistribution / Modifications License

#### ライセンスタイプ(json.extensions.VRM.meta.licenseName)

License Type

* 再配布禁止(Redistribution Prohibited)
* [著作権放棄(CC0)](https://creativecommons.org/publicdomain/zero/1.0/deed.ja)
* [Creative Commons CC BYライセンス(CC_BY)](https://creativecommons.org/licenses/by/4.0/deed.ja)
* [Creative Commons CC BY NCライセンス(CC_BY_NC)](https://creativecommons.org/licenses/by-nc/4.0/deed.ja)
* [Creative Commons CC BY SAライセンス(CC_BY_SA)](https://creativecommons.org/licenses/by-sa/4.0/deed.ja)
* [Creative Commons CC BY NC SAライセンス(CC_BY_NC_SA)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.ja)
* [Creative Commons CC BY NDライセンス(CC_BY_ND)](https://creativecommons.org/licenses/by-nd/4.0/deed.ja)
* [Creative Commons CC BY NC NDライセンス(CC_BY_NC_ND)](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.ja)
* その他(Other)

#### その他ライセンス条件(json.extensions.VRM.meta.otherLicenseUrl)

Other License Url

上記許諾条件以外のライセンス条件がある場合はそのライセンス文書へのURLを記述

# VRM拡張: モーフ設定(json.extensions.VRM.blendShapeMaster)

BlendShapeをグループ化するBlendShapeGroupの配列を設定します。

```js
{
    "title": "vrm.blendshape",
    "type": "object",
    "properties": {
        "blendShapeGroups": {
            "type": "array",
            "items": {
                "$ref": "vrm.blendshape.group.schema.json"
            }
        }
    }
}
```

## ブレンドシェイプグループ(json.extensions.VRM.blendShapeMaster.blendShapeGroups)

```js
{
    "title": "vrm.blendshape.group",
    "type": "object",
    "properties": {
        "name": {
            "description": "Expression name",
            "type": "string"
        },
        "presetName": {
            "description": "Predefined Expression name",
            "type": "string",
            "enum": ["Neutral","A","I","U","E","O","Blink","Joy","Angry","Sorrow","Fun","LookUp","LookDown","LookLeft","LookRight","Blink_L","Blink_R"]
        },
        "binds": {
            "description": "Low level blendshape references.",
            "type": "array",
            "items": {
                "$ref": "vrm.blendshape.bind.schema.json"
            }
        },
        "materialValues": {
            "description": "Material animation references.",
            "type": "array",
            "items": {
                "$ref": "vrm.blendshape.materialbind.schema.json"
            }
        }
    }
}
```

```js
{
    "title": "vrm.blendshape.bind",
    "type": "object",
    "properties": {
        "mesh": {
            "type": "integer"
        },
        "index": {
            "type": "integer"
        },
        "weight": {
            "type": "number"
        }
    }
}
```

```js
{
    "title": "vrm.blendshape.materialbind",
    "type": "object",
    "properties": {
        "materialName": {
            "type": "string"
        },
        "propertyName": {
            "type": "string"
        },
        "targetValue": {
            "type": "array",
            "items": {
                "type": "number"
            }
        }
    }
}
```

### 名前

名前です。事前定義名と同じ(大文字)を推奨します

### 事前定義名

待機状態の表情

* Neutral

リップシンク

* A
* I
* U
* E
* O

瞬き

* Blink
* Blink_L
* Blink_R

喜怒哀楽

* Fun
* Angry
* Sorrow
* Joy

視線制御

* LookUp
* LookDown
* LookLeft
* LookRight

その他

* Unknown

### ブレンドシェイプの名前の識別名

システムからブレンドシェイプを一意に認識する文字列IDを以下のロジックで決定します。

```
// 疑似コード
function GetID(preset, name)
{
  if (Preset != BlendShapePreset.Unknown)
  {
      return preset.ToString().ToUpper();
  }
  else
  {
      return name.ToUpper();
  }
}
```

* ブレンドシェイプIDがユニークになるようにPresetとNameを設定する

# VRM拡張: 一人称設定(json.extensions.VRM.firstPerson)

```js
{
    "title": "vrm.firstperson",
    "type": "object",
    "properties": {
        "firstPersonBone": {
            "description": "The bone whose rendering should be turned off in first-person view. Usually Head is specified.",
            "type": "integer"
        },
        "firstPersonBoneOffset": {
            "description": "The target position of the VR headset in first-person view. It is assumed that an offset from the head bone to the VR headset is added.",
            "type": "object",
            "properties": {
                "x": {
                    "type": "number"
                },
                "y": {
                    "type": "number"
                },
                "z": {
                    "type": "number"
                }
            }
        },
        "meshAnnotations": {
            "description": "Switch display \/ undisplay for each mesh in first-person view or the others.",
            "type": "array",
            "items": {
                "$ref": "vrm.firstperson.meshannotation.schema.json"
            }
        },
        "lookAtTypeName": {
            "description": "Eye controller mode.",
            "type": "string",
            "enum": ["Bone","BlendShape"]
        },
        "lookAtHorizontalInner": {
            "$ref": "vrm.firstperson.degreemap.schema.json"
        },
        "lookAtHorizontalOuter": {
            "$ref": "vrm.firstperson.degreemap.schema.json"
        },
        "lookAtVerticalDown": {
            "$ref": "vrm.firstperson.degreemap.schema.json"
        },
        "lookAtVerticalUp": {
            "$ref": "vrm.firstperson.degreemap.schema.json"
        }
    }
}
```

一人称視点のアバターを描画する場合、自モデルの頭部の中が見えてしまうという問題が生じます[^firstperson]。
これに対応するために、一人称時の表示状態を指定できます。

[^firstperson]: バックフェースカリングや近平面である程度対処できますが、口の中が作りこんであるモデルの歯茎が意図せずに見えてしまうなど不十分な場合があります。

## firstPersonBone(json.extensions.VRM.firstPerson.firstPersonBone)

一人称時に描画を切り替えるべきボーンを指定します。通常``Head``です。

## firstPersonBoneOffset(json.extensions.VRM.firstPerson.firstPersonBoneOffset)

一人時のヘッドセットの目標位置。
頭ボーンからヘッドセットへのオフセットを加味することを想定している。

## meshAnnotations(json.extensions.VRM.firstPerson.meshAnnotations)

各メッシュに対して一人称時とそれ以外で表示・非表示を切り替えることができます。
以下の設定があります。

* Auto: firstPersonBoneとその子孫に対するボーンWeightを持つポリゴンを自動で非表示にする[^firstPersonAuto]
* FirstPersonOnly: 一人称のみ表示
* ThirdPersonOnly: 三人称のみ表示(頭など一人称時に非表示にするメッシュに指定する)
* Both: 特に表示切替をしない

[^firstPersonAuto]: 実行時に自動で非表示部分を削除したモデルを生成します。

## 視線設定

ターゲットの方向を向くようにキャラクタの視線を制御します。

### 目線タイプ(json.extensions.VRM.firstPerson.lookAtTypeName)

* Bone: ボーンにより目線を操作します。
* BlendShape: BlendShapeにより目線を操作します。BlendShapePreset.LookUp, LookDown, LookLeft, LookRightを使います。

### 角度調整

頭と目標物の角度差を目ボーンに適用する場合の角度を調整します。

#### json.extensions.VRM.firstPerson.lookAtHorizontalInner

#### json.extensions.VRM.firstPerson.lookAtHorizontalOuter

#### json.extensions.VRM.firstPerson.lookAtVerticalDown

#### json.extensions.VRM.firstPerson.lookAtVerticalUp

# VRM拡張: 揺れモノ設定(json.extensions.VRM.secondaryAnimation)

尻尾や髪の毛などのひも状のオブジェクトの自動アニメーションの設定です。

## 揺れるボーン(secondaryAnimation.boneGroups)

```js
{
    "title": "vrm.secondaryanimation",
    "type": "object",
    "properties": {
        "boneGroups": {
            "type": "array",
            "items": {
                "$ref": "vrm.secondaryanimation.spring.schema.json"
            }
        },
        "colliderGroups": {
            "type": "array",
            "items": {
                "$ref": "vrm.secondaryanimation.collidergroup.schema.json"
            }
        }
    }
}
```

```js
{
    "title": "vrm.secondaryanimation.spring",
    "type": "object",
    "properties": {
        "comment": {
            "description": "Annotation comment",
            "type": "string"
        },
        "stiffiness": {
            "description": "The resilience of the swaying object (the power of returning to the initial pose).",
            "type": "number"
        },
        "gravityPower": {
            "description": "The strength of gravity.",
            "type": "number"
        },
        "gravityDir": {
            "description": "The direction of gravity. Set (0, -1, 0) for simulating the gravity. Set (1, 0, 0) for simulating the wind.",
            "type": "object",
            "properties": {
                "x": {
                    "type": "number"
                },
                "y": {
                    "type": "number"
                },
                "z": {
                    "type": "number"
                }
            }
        },
        "dragForce": {
            "description": "The resistance (deceleration) of automatic animation.",
            "type": "number"
        },
        "center": {
            "description": "The reference point of a swaying object can be set at any location except the origin. When implementing UI moving with warp, the parent node to move with warp can be specified if you don't want to make the object swaying with warp movement.",
            "type": "integer"
        },
        "hitRadius": {
            "description": "The radius of the sphere used for the collision detection with colliders.",
            "type": "number"
        },
        "bones": {
            "description": "Specify the node index of the root bone of the swaying object.",
            "type": "array",
            "items": {
                "type": "integer"
            }
        },
        "colliderGroups": {
            "description": "Specify the index of the collider group for collisions with swaying objects.",
            "type": "array",
            "items": {
                "type": "integer"
            }
        }
    }
}
```

### 揺れるボーンの根元のボーン(json.extensions.VRM.secondaryAnimation.boneGroups[0].bones)

揺れモノの根元のボーンのノードインデックスを指定します。

### 揺れるボーンと衝突する当たり判定(json.extensions.VRM.secondaryAnimation.boneGroups[0].colliderGroups)

揺れモノに対する衝突判定グループのインデックスを指定します。

### パラメーター

#### center(json.extensions.VRM.secondaryAnimation.boneGroups[0].center)

world原点以外の、揺れモノの基準点を設定できます。
ワープで移動するUIを実装した場合に、ワープ移動で揺れモノを揺らしたくない場合にワープで移動する親ノードを指定できます。

#### dragForce(json.extensions.VRM.secondaryAnimation.boneGroups[0].dragForce)

自動アニメーションの抵抗(減速)です。

#### gravityDir(json.extensions.VRM.secondaryAnimation.boneGroups[0].gravityDir)

重力の方向です。(0, -1, 0)にすると重力に、(1, 0, 0)にすると風のように作用します。

#### gravityPower(json.extensions.VRM.secondaryAnimation.boneGroups[0].gravityPower)

重力の強さです。

#### hitRadius(json.extensions.VRM.secondaryAnimation.boneGroups[0].hitRadius)

Colliderとの当たり判定の半径です

#### stiffness(json.extensions.VRM.secondaryAnimation.boneGroups[0].stiffiness)

揺れモノの復元力(初期姿勢に戻る力)です

## 揺れモノ当たり判定設定(json.extensions.VRM.secondaryAnimation.colliderGroups)

揺れモノと衝突する球を設定します。

```js
{
    "title": "vrm.secondaryanimation.collidergroup",
    "type": "object",
    "properties": {
        "node": {
            "description": "The node of the collider group for setting up collision detections.",
            "type": "integer"
        },
        "colliders": {
            "type": "array",
            "items": {
                "type": "object",
                "properties": {
                    "offset": {
                        "description": "The local coordinate from the node of the collider group.",
                        "type": "object",
                        "properties": {
                            "x": {
                                "type": "number"
                            },
                            "y": {
                                "type": "number"
                            },
                            "z": {
                                "type": "number"
                            }
                        }
                    },
                    "radius": {
                        "description": "The radius of the collider.",
                        "type": "number"
                    }
                }
            }
        }
    }
}
```

### ノード(json.extensions.VRM.secondaryAnimation.colliderGroups[0].node)

当たり判定を設置するノードです。

### ローカル座標(json.extensions.VRM.secondaryAnimation.colliderGroups[0].colliders[1].offset)

当たり判定のノードからのローカル座標です。

### 半径(json.extensions.VRM.secondaryAnimation.colliderGroups[0].colliders[1].radius)

当たり判定の半径です。
