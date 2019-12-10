
0.XX(extensions.VRM)        |        1.0(extensions.VRMC_vrm)        |        値の型・例        |        備考        |
---        |        ---        |        ---        |        ---        |
specVersion            |        specVersion                           |        "0.0"                    |        現状、運用していない        |
exporterVersion        |        廃止して、asset.generator 使う        |        "UniVRM-0.51.0"             |        asset.generatorをUniGLTFのバージョンが使っていたため、UniVRMのバージョン判定に使用している        |
blendShapeMaster.blendShapeGroups[*].name                |        blendShape.blendShapeGroups[*].name                |        "A"        |        lowerBlendShapeClipの名前        |
blendShapeMaster.blendShapeGroups[*].presetName            |        blendShape.blendShapeGroups[*].presetName        |        custom, aa ih ou ee oh, blink, joy, angry, sorrow, fun, lookup, lookDown, lookLeft, lookRight, blinkLeft, blinkRight, neutral        |        lowerCamelCaseに修正、Neutral 削るなら削りたい、blink_L => blinkLeft, blink_R => blinkRight, unknown => custom        |
blendShapeMaster.blendShapeGroups[*].isBinary            |        blendShape.blendShapeGroups[*].isBinary            |        bool    |        表情の２値フラグ        |
blendShapeMaster.blendShapeGroups[*].binds[*]            |        blendShape.blendShapeGroups[*].binds[*]            |        []        |                |
blendShapeMaster.blendShapeGroups[*].binds[*].mesh        |        blendShape.blendShapeGroups[*].binds[*].mesh    |        int(mesh index_            |        MeshのIndex        |
blendShapeMaster.blendShapeGroups[*].binds[*].index        |        blendShape.blendShapeGroups[*].binds[*].index    |        int(morphTarget index)    |        MorphTargetのIndex。すべてのprimitiveが同じmorphを持つことを要求している        |
blendShapeMaster.blendShapeGroups[*].binds[*].weight    |        blendShape.blendShapeGroups[*].binds[*].weight    |        int(0-100) => float(0-1)|        MorphTargetの適用率(0-100)。0-1の方がよくないか？        |
blendShapeMaster.blendShapeGroups[*].materialValues[*].materialName        |        blendShape.blendShapeGroups[*].materialValues[*].material        |        "material0" => int(material index)        |        Materialの名前。Editor実装時のpulldownに名前を表示しようとしたのがそのまま出てる、uint32 materialに変更？        |
blendShapeMaster.blendShapeGroups[*].materialValues[*].propertyName        |        廃止        |        "color" => "pbrMetallicRoughness.baseColorFactor"        |        Dictionaryに値をダンプするのをやめたのでProperty指定はできなくなった        |
blendShapeMaster.blendShapeGroups[*].materialValues[*].targetValue        |        blendShape.blendShapeGroups[*].materialValues[*].targetValue        |        float[4]        |        型は Vector4もしくはVector2？        |
blendShape.blendShapeGroups[*].materialValues[*].type        |        color, uvScale, uvOffset        |        メインカラー変更とテクスチャーUV+Offset変更(全テクスチャ一括)        |
blendShape.blendShapeGroups[*].ignoreBlink        |        bool        |                |
blendShape.blendShapeGroups[*].ignoreLookAt        |        bool        |                |
blendShape.blendShapeGroups[*].ignoreMouth        |        bool        |                |
||||
firstPerson.firstPersonBone                        |        HEAD以外ありうるか？        |        76                  |        １人称に対応するボーンを意図している。Head しかありえないと思われる        |
firstPerson.firstPersonBoneOffset                |        firstPerson.firstPersonBoneOffset                    |        {} => float[3] 右手系に        |        VRMのヘッドセット位置や、LookAtの基準位置。firstPersonBoneからの位置オフセット        |
firstPerson.meshAnnotations[*].firstPersonFlag    |        firstPerson.meshAnnotations[*].firstPersonType        |        auto, both, thirdPersonOnly, firstPersonOnly        |        対象meshの１人称描画フラッグ, lowerCamelCaseに修正        |
firstPerson.meshAnnotations[*].mesh                |        firstPerson.meshAnnotations[*].mesh                    |        int(mesh index)        |        対象meshのindex        |
||||
firstPerson.lookAtTypeName                    |        lookAt.LookAtType                            |        bone, blendShape|        lowerCamelCaseに修正        |
firstPerson.lookAtHorizontalInner.curve        |        廃止                                        |        float［8］        |        UnityのCurveのdump、lookAtとして独立。設定として過剰なような。削除？        |
firstPerson.lookAtHorizontalInner.xRange    |        lookAt.lookAtHorizontalInner.inputMaxValue    |                        |        入力値の値域。clampされる        |
firstPerson.lookAtHorizontalInner.yRange    |        lookAt.lookAtHorizontalInner.outputScale    |                        |        出力値の係数。乗算される。bone時は回転角度のradians、blendShape時はweight値[0-1]        |
firstPerson.lookAtHorizontalOuter.curve        |        廃止                                        |        float［8］        |        UnityのCurveのdump                |
firstPerson.lookAtHorizontalOuter.xRange    |        lookAt.lookAtHorizontalOuter.inputMaxValue    |                        |        入力値の値域。clampされる        |
firstPerson.lookAtHorizontalOuter.yRange    |        lookAt.lookAtHorizontalOuter.outputScale    |                        |        出力値の係数。乗算される        |
firstPerson.lookAtVerticalDown.curve        |        廃止                                        |        float［8］        |        UnityのCurveのdump                |
firstPerson.lookAtVerticalDown.xRange        |        lookAt.lookAtVerticalDown.inputMaxValue        |                        |        入力値の値域。clampされる        |
firstPerson.lookAtVerticalDown.yRange        |        lookAt.lookAtVerticalDown.outputScale        |                        |        出力値の係数。乗算される        |
firstPerson.lookAtVerticalUp.curve            |        廃止                                        |        float［8］        |        UnityのCurveのdump                |
firstPerson.lookAtVerticalUp.xRange            |        lookAt.lookAtVerticalUp.inputMaxValue        |                        |        入力値の値域。clampされる        |
firstPerson.lookAtVerticalUp.yRange            |        lookAt.lookAtVerticalUp.outputScale            |                        |        出力値の係数。乗算される        |
||||
humanoid.legStretch                            |        廃止                            |        0                    |                |
humanoid.armStretch                            |        廃止                            |        0                    |                |
humanoid.feetSpacing                        |        廃止                            |        0                    |                |
humanoid.lowerArmTwist                        |        廃止                            |        0                    |                |
humanoid.lowerLegTwist                        |        廃止                            |        0                    |                |
humanoid.upperArmTwist                        |        廃止                            |        0                    |                |
humanoid.upperLegTwist                        |        廃止                            |        0                    |                |
humanoid.hasTranslationDoF                    |        廃止                            |        FALSE                |                |
humanoid.humanBones[*].bone                    |        humanoid.humanBones[*].bone        |        enum "hips"            |                |
humanoid.humanBones[*].node                    |        humanoid.humanBones[*].node        |        int(node index)        |                |
humanoid.humanBones［*］.axisLength            |        廃止                            |        1                    |                |
humanoid.humanBones［*］.center                |        廃止                            |        {}                    |                |
humanoid.humanBones［*］.max                |        廃止                            |        {}                    |                |
humanoid.humanBones［*］.min                |        廃止                            |        {}                    |                |
humanoid.humanBones［*］.useDefaultValues    |        廃止                            |        TRUE                |                |
||||
materialProperties                                            |        👉extensions.VRMC_materials_mtoon        |        {}        |        extensions.VRMC_materials_mtoon として独立        |
materialProperties.shader.VRM/UnlitTransparentZWrite        |        廃止                                     |                    |                                                        |
||||
meta.allowedUserName        |        meta.allowedUser        |        enum "Everyone"                                    |                |
meta.author                    |        meta.author                |        "© DWANGO Co., Ltd."                            |                |
meta.commercialUssageName    |        meta.commercialUsage    |        enum "Allow" => bool ? URLを使うときの扱い？      |        タイポ        |
meta.contactInformation        |        meta.contactInformation    |        "https://3d.nicovideo.jp/alicia/"                |                |
meta.licenseName            |        meta.license            |        enum "Other"                                    |                |
meta.otherLicenseUrl        |        meta.otherLicenseUrl    |        "https://3d.nicovideo.jp/alicia/rule.html"        |                |
meta.otherPermissionUrl        |        meta.otherPermissionUrl    |        "https://3d.nicovideo.jp/alicia/rule.html"        |                |
meta.reference                |        meta.reference            |        ""                                                |                |
meta.sexualUssageName        |        meta.sexualUsage        |        enum "Allow" => bool ? URLを使うときの扱い？      |        タイポ        |
meta.texture                |        meta.thumbnailImage        |        int(texture index) => int(image index)            |                |
meta.title                    |        meta.title                |        "Alicia Solid"                                    |                |
meta.version                |        meta.version            |        "1.10"                                            |                |
meta.violentUssageName        |        meta.violentUsage        |        enum "Allow" => bool ? URLを使うときの扱い？      |        タイポ        |
||||
secondaryAnimation                                            |        springBone                                                |                                |                |
secondaryAnimation.boneGroups［*］.bones                    |        springBone.boneGroups[*].bones                            |        int[]                    |        スプリングの始点となるボーンを複数指定する        |
secondaryAnimation.boneGroups［*］.center                    |        springBone.boneGroups[*].center                            |    int(node index)                |        スプリングを解決するローカル空間を指定する(デフォルトはワールド)        |
secondaryAnimation.boneGroups［*］.colliderGroups            |        springBone.boneGroups[*].colliderGroups                    |        int[]                    |        ColliderGroupのindexの集合        |
secondaryAnimation.boneGroups［*］.comment                    |        springBone.boneGroups[*].comment                        |        “comment”                |        comment => nameに変更？        |
secondaryAnimation.boneGroups［*］.dragForce                |        springBone.boneGroups[*].dragForce                        |        float                    |                |
secondaryAnimation.boneGroups［*］.gravityDir                |        springBone.boneGroups[*].gravityDir                        |        {} => float[3] 右手系に     |                |
secondaryAnimation.boneGroups［*］.gravityPower                |        springBone.boneGroups[*].gravityPower                    |        float                    |                |
secondaryAnimation.boneGroups［*］.hitRadius                |        springBone.boneGroups[*].hitRadius                        |        float                    |                |
secondaryAnimation.boneGroups［*］.stiffiness                |        springBone.boneGroups[*].stiffness                        |        float                    |        タイポ        |
secondaryAnimation.colliderGroups［*］.colliders［*］.offset|        springBone.colliderGroups[*].colliders[*].offset        |        {} => float[3] 右手系に     |                |
secondaryAnimation.colliderGroups［*］.colliders［*］.radius|        springBone.colliderGroups[*].colliders[*].size            |        float[]                    |        shapeTypeによって内容が変わる。sphreの時は float[1](radius)、capsuleの時はfloat[2](radius, length)        |
springBone.colliderGroups[*].colliders[*].shapeType            |        sphere, capsule                |                |
springBone.colliderGroups[*].colliders[*].rotation            |        float[3]                    |        euler角radians        |
secondaryAnimation.colliderGroups［*］.node        |        springBone.colliderGroups[*].node        |        int(node index)        |                |
||||
constraint        |                |        仕様検討中        |                |
