# `VRMC_vrm.meta`

In this document, `meta` field of `VRMC_vrm` extension will be explained.

## glTF Schema Updates

### Properties

| Name                           | Value      | Description                                                           | Required                         |
|:-------------------------------|:-----------|:----------------------------------------------------------------------|:---------------------------------|
| name                           | `string`   | The name of the model                                                 | ✅ Yes                           |
| version                        | `string`   | The version of the model                                              | No                               |
| authors                        | `string[]` | Authors of the model                                                  | ✅ Yes                           |
| copyrightInformation           | `string`   | An information that describes the copyright of the model              | No                               |
| contactInformation             | `string`   | An information that describes the contact information of the author   | No                               |
| references                     | `string[]` | References / original works of the model                              | No                               |
| thirdPartyLicenses             | `string`   | Third party licenses of the model                                     | No                               |
| thumbnailImage                 | `integer`  | The index to access the thumbnail image of the model                  | No                               |
| avatarPermission               | `string`   | A person who can perform as an avatar with this model                 | No, default: `OnlyAuthor`        |
| allowExcessivelyViolentUsage   | `boolean`  | Perform violent acts with this model                                  | No, default: `false`             |
| allowExcessivelySexualUsage    | `boolean`  | Perform sexual acts with this model                                   | No, default: `false`             |
| commercialUsage                | `string`   | Commercial use                                                        | No, default: `personalNonProfit` |
| allowPoliticalOrReligiousUsage | `boolean`  | Permission for political or religious purposes                        | No, default: `false`             |
| creditNotation                 | `string`   | An option that forces or abandons to display the credit of this model | No, default: `required`          |
| allowRedistribution            | `boolean`  | A flag that permits to redistribute this model                        | No                               |
| modification                   | `string`   | An option that controls the condition to modify this model            | No, default: `prohibited`        |
| otherLicenseUrl                | `string`   | Describe the URL links of other license                               | No                               |

### meta.name ✅

The name of the model.

- Type: `string`
- Required: Yes

### meta.version

The version of the model.

- Type: `string`
- Required: No

### meta.authors ✅

Authors of the model.

It must have at least one entry that is not an empty string.
Putting model creator/first author name at the beginning is recommended.

- Type: `string[]`
- Required: Yes

### meta.copyrightInformation

An information that describes the copyright of the model.

Since the property is intended to be used to display the copyright holder of the model, It must be distinguished from the previous property `authors`.

- Type: `string`
- Required: No

### meta.contactInformation

An information that describes the contact information of the author.

- Type: `string`
- Required: No

### meta.references

References / original works of the model.

- Type: `string[]`
- Required: No

### meta.thirdPartyLicenses

Third party licenses of the model, if required.

You can use line breaks in this property.

- Type: `string`
- Required: No

### meta.thumbnailImage

The index to access the thumbnail image of the model in `gltf.images` .

The thumbnail image must be square. Preferable resolution is 1024 x 1024.
For the convenience, the image must be either jpg or png that are supported in glTF by its core specification.
Use of other formats using extensions are not allowed.

Intended to be used in applications that uses VRMs, as an icon of the model.

- Type: `integer`
- Required: No
- Minimum: `>= 0`

### meta.avatarPermission

A person who can perform as an avatar with this model.

`onlyAuthor` indicates people cannot use this model as an avatar unless you are the author itself.
`explicitlyLicensedPerson` indicates people who are explicitly licensed can use this model as an avatar. Intended to be used when the model is a paid model, for example.
`everyone` indicates anyone can use this model as an avatar.

- Type: `string`
- Required: No, default: `onlyAuthor`
- Allowed values:
  - `onlyAuthor`
  - `explicitlyLicensedPerson`
  - `everyone`

### meta.allowExcessivelyViolentUsage

A flag that permits to use this model in excessively violent contents.

- Type: `boolean`
- Required: No, default: `false`

### meta.allowExcessivelySexualUsage

A flag that permits to use this model in excessively sexual contents.

- Type: `boolean`
- Required: No, default: `false`

### meta.commercialUsage

An option that permits to use this model in commercial products.

`personalNonProfit` indicates personal users can use this model only if they are not going to make a profit using this model.
`personalProfit` indicates personal users can use this model regardless they are going to make a profit using this model or not.
Corporation users cannot use this model unless the property is `corporation`.

- Type: `string`
- Required: No, default: `personalNonProfit`
- Allowed values:
  - `personalNonProfit`
  - `personalProfit`
  - `corporation`

### meta.allowPoliticalOrReligiousUsage

A flag that permits to use this model in political or religious contents.

- Type: `boolean`
- Required: No, default: `false`

### meta.creditNotation

An option that forces or abandons to display the credit of this model.

When the property is `required`, users of the model must show the credit of the model.
When the property is `unnecessary`, users of the model do not have to show the credit of the model.
`abandoned` indicates the holder of the model abandoned their right of the credit.

- Type: `string`
- Required: No, default: `required`
- Allowed values:
  - `required`
  - `unnecessary`
  - `abandoned`

### meta.allowRedistribution

A flag that permits to redistribute this model.

- Type: `boolean`
- Required: No, default: `false`

### meta.modification

An option that gives a permission to modify this model, or redistribute the modified model.

When the property is `prohibited`, users cannot modify this model.
When the property is either `allowModification` or `allowModificationRedistribution`, users can modify this model.
When the property is `allowModificationRedistribution`, users can redistribute the modified model.

- Type: `string`
- Required: No, default: `prohibited`
- Allowed values:
  - `prohibited`
  - `allowModification`
  - `allowModificationRedistribution`

### meta.otherLicenseUrl

Describe the URL links of other license.

- Type: `string`
- Required: No
