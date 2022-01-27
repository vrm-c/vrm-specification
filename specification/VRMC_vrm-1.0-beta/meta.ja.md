# `VRMC_vrm.meta`

本文書では、 `VRMC_vrm` 拡張のうち `meta` フィールドについての仕様を示します。

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Meta](#meta)
- [ライセンス](#%E3%83%A9%E3%82%A4%E3%82%BB%E3%83%B3%E3%82%B9)
- [glTF Schema Updates](#gltf-schema-updates)
  - [プロパティ](#%E3%83%97%E3%83%AD%E3%83%91%E3%83%86%E3%82%A3)
  - [meta.name ✅](#metaname-)
  - [meta.version](#metaversion)
  - [meta.authors ✅](#metaauthors-)
  - [meta.copyrightInformation](#metacopyrightinformation)
  - [meta.contactInformation](#metacontactinformation)
  - [meta.references](#metareferences)
  - [meta.thirdPartyLicenses](#metathirdpartylicenses)
  - [meta.thumbnailImage](#metathumbnailimage)
  - [meta.licenseUrl ✅](#metalicenseurl-)
  - [meta.avatarPermission](#metaavatarpermission)
  - [meta.allowExcessivelyViolentUsage](#metaallowexcessivelyviolentusage)
  - [meta.allowExcessivelySexualUsage](#metaallowexcessivelysexualusage)
  - [meta.commercialUsage](#metacommercialusage)
  - [meta.allowPoliticalOrReligiousUsage](#metaallowpoliticalorreligioususage)
  - [meta.allowAntisocialOrHateUsage](#metaallowantisocialorhateusage)
  - [meta.creditNotation](#metacreditnotation)
  - [meta.allowRedistribution](#metaallowredistribution)
  - [meta.modification](#metamodification)
  - [meta.otherLicenseUrl](#metaotherlicenseurl)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Meta

VRMの `meta` フィールドでは、モデルに関するメタ情報を記述することができます。
メタ情報には、モデルの名前や作者などの基本的な情報に加え、モデルの利用条件に関わる情報を記述することができます。

## ライセンス

VRM拡張では、モデルのライセンス情報を `meta` フィールドに記述することができます。

`meta` フィールドに格納されるライセンス情報は、ライセンス文書へのURLとライセンス設定によって構成されます。
ライセンス文書は、VRMコンソーシアムによって制定されたVRMパブリック・ライセンス文書を指し、 `meta.licenseUrl` に文書へのユニークなURLが格納されている必要があります。
ライセンス設定は、ライセンス文書から参照される個別の設定であり、モデルのライセンサーが自由に指定することができます。

### licenseUrl

`licenseUrl` プロパティは、VRM1.0においては `https://vrm.dev/licenses/1.0/` を許容します。

## glTF Schema Updates

### プロパティ

| 名前                           | 値         | 説明                                           | 必須                            |
|:-------------------------------|:-----------|:-----------------------------------------------|:--------------------------------|
| name                           | `string`   | モデルの名前                                       | ✅ Yes                           |
| version                        | `string`   | モデルのバージョン                                      | No                              |
| authors                        | `string[]` | モデルの作者名                                     | ✅ Yes                           |
| copyrightInformation           | `string`   | モデルの著作権者                                   | No                              |
| contactInformation             | `string`   | モデルの作者（代表者）への連絡先                       | No                              |
| references                     | `string[]` | モデルの「親作品」となるようなものがあれば、その情報                | No                              |
| thirdPartyLicenses             | `string`   | モデルのサードパーティライセンス表記                           | No                              |
| thumbnailImage                 | `integer`  | モデルのサムネイルとなる画像へのインデックス                       | No                              |
| licenseUrl                     | `string`   | このモデルが参照するVRMライセンス文書へのURL                  | ✅ Yes                           |
| avatarPermission               | `string`   | このモデルに人格を与えることの許諾範囲                     | No, 初期値: `OnlyAuthor`        |
| allowExcessivelyViolentUsage   | `boolean`  | このモデルの過剰な暴力表現を含むコンテンツでの利用を許可するか     | No, 初期値: `false`             |
| allowExcessivelySexualUsage    | `boolean`  | このモデルの過剰な性的表現を含むコンテンツでの利用を許可するか     | No, 初期値: `false`             |
| commercialUsage                | `string`   | このモデルを利用した商用利用の許可範囲                  | No, 初期値: `personalNonProfit` |
| allowPoliticalOrReligiousUsage | `boolean`  | このモデルの政治・宗教用途での利用を許可するか              | No, 初期値: `false`             |
| allowAntisocialOrHateUsage     | `boolean`  | このモデルの反社会的・憎悪表現を含むコンテンツでの利用を許可するか | No, 初期値: `false`             |
| creditNotation                 | `string`   | このモデルのクレジット表記の強制および放棄の指定               | No, 初期値: `required`          |
| allowRedistribution            | `boolean`  | このモデルの再配布を許可するか                           | No, 初期値: `false`             |
| modification                   | `string`   | このモデルの改変の許可範囲                            | No, 初期値: `prohibited`        |
| otherLicenseUrl                | `string`   | その他のライセンス条件があれば、そのURL                       | No                              |

### meta.name ✅

モデルの名前です。

- 型: `string`
- 必須: Yes

### meta.version

モデルのバージョンです。

- 型: `string`
- 必須: No

### meta.authors ✅

モデルの作者名です。

必ず、一つ以上の空文字でない項目が必要です。
アバターの造形者・代表者名を最初に書くことを推奨します。

- 型: `string[]`
- 必須: Yes

### meta.copyrightInformation

モデルの著作権者です。

このモデルの著作権を表示するために使われることを想定しており、直前の `authors` とは明確に区別して扱います。

- 型: `string`
- 必須: No

### meta.contactInformation

モデルの作者（代表者）への連絡先です。

- 型: `string`
- 必須: No

### meta.references

モデルの「親作品」となるようなものがあれば、その情報を記述します。

- 型: `string[]`
- 必須: No

### meta.thirdPartyLicenses

モデルのサードパーティライセンス表記が必要であれば、ここに記述します。

改行を用いて、複数行に渡る文書を記述可能です。

- 型: `string`
- 必須: No

### meta.thumbnailImage

モデルのサムネイルを、 `gltf.images` に定義されたインデックスを用いて指定します。

画像は正方形である必要があります。解像度は 1024x1024 を推奨します。
利便性のため、画像は glTF が標準でサポートする jpg もしくは png である必要があります。
拡張を用いて他形式を用いることは許可されません。

VRMを利用するアプリケーションが、モデルのアイコンとして表示することを想定しています。

- 型: `integer`
- 必須: No
- 最小値: `>= 0`

### meta.licenseUrl ✅

このモデルが参照する、ライセンス文書へのURLを指定します。
VRMパブリック・ライセンス文書へのユニークなURLが格納されている必要があります。

- 型: `string`
- 必須: Yes

### meta.avatarPermission

このモデルをアバターとして操作し演じることを許可するユーザを指定します。

`onlyAuthor` は、モデルの作者のみにアバターとしての操作が許可されることを示します。
`onlySeparatelyLicensedPerson` は、別の文書でライセンス許諾されたユーザにアバターとしての操作が許可されることを示します。例えば、有料で販売されるモデルに対して用いられることを想定しています。
`everyone` は、誰にでもこのモデルをアバターとして操作することが許可されることを示します。

- 型: `string`
- 必須: No, 初期値: `onlyAuthor`
- 許可された値:
  - `onlyAuthor`
  - `onlySeparatelyLicensedPerson`
  - `everyone`

### meta.allowExcessivelyViolentUsage

このモデルを、過度の暴力表現を含むコンテンツに利用することを許可するか否かを指定します。

- 型: `boolean`
- 必須: No, 初期値: `false`

### meta.allowExcessivelySexualUsage

このモデルを、過度の性的表現を含むコンテンツに利用することを許可するか否かを指定します。

- 型: `boolean`
- 必須: No, 初期値: `false`

### meta.commercialUsage

このモデルを商用利用することを許可するかを指定します。

`personalNonProfit` は、個人のユーザが非商用目的に限り利用することを許可することを示します。
`personalProfit` は、個人のユーザが商用・非商用のどちらの目的に関わらず利用することを許可することを示します。
法人のユーザは、このプロパティが `corporation` でない限り、該当するモデルの利用が許可されません。

- 型: `string`
- 必須: No, 初期値: `personalNonProfit`
- 許可された値:
  - `personalNonProfit`
  - `personalProfit`
  - `corporation`

### meta.allowPoliticalOrReligiousUsage

このモデルを、政治的・宗教的なコンテンツに対して利用することを許可するか否かを指定します。

- 型: `boolean`
- 必須: No, 初期値: `false`

### meta.allowAntisocialOrHateUsage

このモデルを、反社会的・憎悪表現を含むコンテンツに対して利用することを許可するか否かを指定します。

- 型: `boolean`
- 必須: No, 初期値: `false`

### meta.creditNotation

このモデルのクレジット表記を要求することを指定します。

このプロパティが `required` の場合、ユーザはモデルのクレジット表記を必ずしなければなりません。
このプロパティが `unnecessary` の場合、ユーザはモデルのクレジット表記を必ずする必要はありません。

- 型: `string`
- 必須: No, 初期値: `required`
- 許可された値:
  - `required`
  - `unnecessary`

### meta.allowRedistribution

このモデルを再配布することを許可するか否かを指定します。

- 型: `boolean`
- 必須: No, 初期値: `false`

### meta.modification

このモデルを改変することを許可するか否か、および改変したモデルの再配布を許可するか否かを指定します。

このプロパティが `prohibited` の場合、モデルの改変を許可しないことを示します。
このプロパティが `allowModification` もしくは `allowModificationRedistribution` の場合、モデルの改変を許可することを示します。
このプロパティが `allowModificationRedistribution` の場合、改変したモデルの再配布を許可することを示します。

- 型: `string`
- 必須: No, 初期値: `prohibited`
- 許可された値:
  - `prohibited`
  - `allowModification`
  - `allowModificationRedistribution`

### meta.otherLicenseUrl

その他のライセンス条件があれば、そのURLを指定します。

- 型: `string`
- 必須: No
