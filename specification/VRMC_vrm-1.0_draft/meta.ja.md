# `VRMC_vrm.meta`

本文書では、 `VRMC_vrm` 拡張のうち `meta` フィールドについての仕様を示します。

## モデル名・作者など

| 名前               | 値                     | 備考                                                                                                                                                                      |
|:-------------------|:-----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name               | string 必須            | アバターモデル名称                                                                                                                                                        |
| version            | string 必須            | アバターモデルのバージョン                                                                                                                                                |
| authors            | string[] 必須          | 作者名（複数名を併記可）。アバターの造形者 ・代表者名を最初に書くことを推奨、複数系（s）で名称を表記                                                                      |
| copyrights         | string                 | 著作権者。Authorsと明確に分ける                                                                                                                                           |
| contactInformation | string                 | 作者（代表者）への連絡先                                                                                                                                                  |
| reference          | string                 | アバターの「親作品」となるようなものがあれば、その情報                                                                                                                    |
| thumbnailImage     | gltf.images への index | アバターモデルのサムネイルにアクセスするためのgltf.imagesへのindex。1024x1024程度の解像度テクスチャを推奨します。正方形の画像は必須です。アプリのアイコンとして使かいます |

## アバターの人格に関する許容範囲

| 名前                      | 値                                                                                           | 備考                                           |
|---------------------------|----------------------------------------------------------------------------------------------|------------------------------------------------|
| avatarPermission          | OnlyAuthor, ExplicitlyLicensedPerson, Everyone                                               | アバターに人格を与えることの許諾範囲           |
| violentUsage              | bool                                                                                         | このアバターを用いて暴力表現を演じることの許可 |
| sexualUsage               | bool                                                                                         | このアバターを用いて性的表現を演じることの許可 |
| gameUsage                 | bool                                                                                         | アバターを操作することの許諾範囲               |
| commercialUsage           | PersonalNonCommercialNonProfit, PersonalNonCommercialProfit, PersonalCommercial, Corporation | 商用利用の許可                                 |
| politicalOrReligiousUsage | bool                                                                                         | 政治的・宗教的目的での利用                     |

### otherPermissionUrl
上記許諾条件以外のライセンス条件がある場合はそのライセンス文書へのURLを記述

## 再配布・改変に関する許容範囲

| 名前                | 値                                | 備考           |
|:--------------------|:----------------------------------|----------------|
| creditNotation      | Required,Unnecessary,Abandoned    | クレジット表記 |
| allowRedistribution | bool                              | 再配布         |
| modify              | Prohibited,Inherited,NotInherited | 改変           |
