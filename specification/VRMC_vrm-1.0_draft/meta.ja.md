# `VRMC_vrm.meta`

本文書では、 `VRMC_vrm` 拡張のうち `meta` フィールドについての仕様を示します。

## glTF Schema Updates

### プロパティ

| 名前                           | 値         | 説明                                       | 必須                            |
|:-------------------------------|:-----------|:-------------------------------------------|:--------------------------------|
| name                           | `string`   | モデルの名前                                   | ✅ Yes                           |
| version                        | `string`   | モデルのバージョン                                  | No                              |
| authors                        | `string[]` | モデルの作者名                                 | ✅ Yes                           |
| copyrightInformation           | `string`   | モデルの著作権者                               | No                              |
| contactInformation             | `string`   | モデルの作者（代表者）への連絡先                   | No                              |
| references                     | `string[]` | モデルの「親作品」となるようなものがあれば、その情報            | No                              |
| thirdPartyLicenses             | `string`   | モデルのサードパーティライセンス表記                       | No                              |
| thumbnailImage                 | `integer`  | モデルのサムネイルとなる画像へのインデックス                   | No                              |
| avatarPermission               | `string`   | このモデルに人格を与えることの許諾範囲                 | No, 初期値: `OnlyAuthor`        |
| allowExcessivelyViolentUsage   | `boolean`  | このモデルの過剰な暴力表現を含むコンテンツでの利用を許可するか | No, 初期値: `false`             |
| allowExcessivelySexualUsage    | `boolean`  | このモデルの過剰な性的表現を含むコンテンツでの利用を許可するか | No, 初期値: `false`             |
| commercialUsage                | `string`   | このモデルを利用した商用利用の許可範囲              | No, 初期値: `personalNonProfit` |
| allowPoliticalOrReligiousUsage | `boolean`  | このモデルの政治・宗教用途での利用を許可するか          | No, 初期値: `false`             |
| creditNotation                 | `string`   | このモデルのクレジット表記の強制および放棄の指定           | No, 初期値: `required`          |
| allowRedistribution            | `boolean`  | このモデルの再配布を許可するか                       | No                              |
| modification                   | `string`   | このモデルの改変の許可範囲                        | No, 初期値: `prohibited`        |
| otherLicenseUrl                | `string`   | その他のライセンス条件があれば、そのURL                   | No                              |

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

VRMを利用するアプリケーションが、モデルのアイコンとして表示することを想定しています。

- 型: `integer`
- 必須: No
- 最小値: `>= 0`

### meta.avatarPermission

このモデルをアバターとして操作し演じることを許可するユーザを指定します。

`onlyAuthor` は、モデルの作者のみにアバターとしての操作が許可されることを示します。
`explicitlyLicensedPerson` は、明示的にライセンス許諾されたユーザにアバターとしての操作が許可されることを示します。例えば、有料で販売されるモデルに対して用いられることを想定しています。
`everyone` は、誰にでもこのモデルをアバターとして操作することが許可されることを示します。

- 型: `string`
- 必須: No, 初期値: `onlyAuthor`
- 許可された値:
  - `onlyAuthor`
  - `explicitlyLicensedPerson`
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

### meta.creditNotation

このモデルのクレジット表記を強制および放棄することを指定します。

このプロパティが `required` の場合、ユーザはモデルのクレジット表記を必ずしなければなりません。
このプロパティが `unnecessary` の場合、ユーザはモデルのクレジット表記を必ずする必要はありません。
`abandoned` は、モデルのクレジット表記を放棄することを指定します。

- 型: `string`
- 必須: No, 初期値: `required`
- 許可された値:
  - `required`
  - `unnecessary`
  - `abandoned`

### meta.allowRedistribution

このモデルを再配布することを許可するか否かを指定します。

- 型: `boolean`
- 必須: No, 初期値: `false`

### meta.modification

このモデルを改変することの条件を指定します。

このプロパティが `prohibited` の場合、モデルの改変を許可しないことを示します。
このプロパティが `inherited` もしくは `notInherited` の場合、モデルの改変を許可することを示します。
ユーザがこのプロパティが `inherited` のモデルを改変した場合、改変したモデルは元のモデルのライセンス条件を継承する必要があります。

- 型: `string`
- 必須: No, 初期値: `prohibited`
- 許可された値:
  - `prohibited`
  - `inherited`
  - `notInherited`

### meta.otherLicenseUrl

その他のライセンス条件があれば、そのURLを指定します。

- 型: `string`
- 必須: No
