# `VRMC_vrm.meta`

In this document, the `meta` field of `VRMC_vrm` extension will be explained.

## Meta

In the `meta` field of VRM, you can describe meta information about the model.
`meta` contains not only basic model information like the name and authors of the model but also information about the use conditions of the model.

## License

In the VRM extension, license information will be specified in the `meta` field.

The license information of `meta` consists of a URL of a license document and license settings.
The license document is a document of VRM Public License established by VRM Consortium. `meta.licenseUrl` field must have a unique URL towards the document.
License settings are settings that are referred from the license document and a licenser of the model can specify.

### licenseUrl

The property `licenseUrl` accepts `https://vrm.dev/licenses/1.0/` in VRM1.0.

## glTF Schema Updates

### Properties

| Name                           | Value      | Description                                                                | Required                         |
|:-------------------------------|:-----------|:---------------------------------------------------------------------------|:---------------------------------|
| name                           | `string`   | The name of the model                                                      | ✅ Yes                            |
| version                        | `string`   | The version of the model                                                   | No                               |
| authors                        | `string[]` | Authors of the model                                                       | ✅ Yes                            |
| copyrightInformation           | `string`   | Information that describes the copyright of the model                      | No                               |
| contactInformation             | `string`   | The contact information of the author                                      | No                               |
| references                     | `string[]` | References / original works of the model                                   | No                               |
| thirdPartyLicenses             | `string`   | Third party licenses of the model                                          | No                               |
| thumbnailImage                 | `integer`  | The index to access the thumbnail image of the model                       | No                               |
| licenseUrl                     | `string`   | A URL towards the license document this model refers                       | ✅ Yes                            |
| avatarPermission               | `string`   | The person who can act as an avatar with this model                        | No, default: `OnlyAuthor`        |
| allowExcessivelyViolentUsage   | `boolean`  | Perform violent acts with this model                                       | No, default: `false`             |
| allowExcessivelySexualUsage    | `boolean`  | Perform sexual acts with this model                                        | No, default: `false`             |
| commercialUsage                | `string`   | Commercial use                                                             | No, default: `personalNonProfit` |
| allowPoliticalOrReligiousUsage | `boolean`  | Permission for political or religious purposes                             | No, default: `false`             |
| allowAntisocialOrHateUsage     | `boolean`  | Permission for content that contains anti-social activities or hate speech | No, default: `false`             |
| creditNotation                 | `string`   | An option that forces or omits displaying the credit of this model.        | No, default: `required`          |
| allowRedistribution            | `boolean`  | A flag that permits redistribution of this model                           | No, default: `false`             |
| modification                   | `string`   | An option that controls the condition for modifying this mode              | No, default: `prohibited`        |
| otherLicenseUrl                | `string`   | Describe the URL links of other license                                    | No                               |

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

Must have at least one entry that is not an empty string.
Putting model creator/first author name at the beginning is recommended.

- Type: `string[]`
- Required: Yes

### meta.copyrightInformation

Information that describes the copyright of the model.

Since this property is intended to be used to display the copyright holder of the model, it must be distinguished from the previous property `authors`.

- Type: `string`
- Required: No

### meta.contactInformation

Information that describes the contact information of the author.

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
For convenience, the image must be either jpg or png that is supported in glTF by its core specification.
Use of other formats using extensions is not allowed.

Intended to be used in applications that uses VRMs, as an icon of the model.

- Type: `integer`
- Required: No
- Minimum: `>= 0`

### meta.licenseUrl ✅

A URL towards the license document this model refers to.
It must be a unique URL to VRM Public License.

- Type: `string`
- Required: Yes

### meta.avatarPermission

The person who can perform as an avatar with this model.

`onlyAuthor` indicates people cannot use this model as an avatar unless they are the author.
`onlySeparatelyLicensedPerson` indicates that people who are licensed in separated documents can use this model as an avatar. Intended to be used when the model is a paid model, for example.
`everyone` indicates anyone can use this model as an avatar.

- Type: `string`
- Required: No, default: `onlyAuthor`
- Allowed values:
  - `onlyAuthor`
  - `onlySeparatelyLicensedPerson`
  - `everyone`

### meta.allowExcessivelyViolentUsage

A flag that permits use of this model in excessively violent content.

- Type: `boolean`
- Required: No, default: `false`

### meta.allowExcessivelySexualUsage

A flag that permits use of this model in excessively sexual content.

- Type: `boolean`
- Required: No, default: `false`

### meta.commercialUsage

An option that permits use of this model in commercial products.

`personalNonProfit` indicates personal users can use this model only if they are not going to make a profit from its use.
`personalProfit` indicates personal users can use this model whether they are going to make a profit from its use or not.
Corporate organization users cannot use this model unless the property is `corporation`.

- Type: `string`
- Required: No, default: `personalNonProfit`
- Allowed values:
  - `personalNonProfit`
  - `personalProfit`
  - `corporation`

### meta.allowPoliticalOrReligiousUsage

A flag that permits use of the model in political or religious content.

- Type: `boolean`
- Required: No, default: `false`

### meta.allowAntisocialOrHateUsage

A flag that permits use of this model in content that contains anti-social activities or hate speech.

- Type: `boolean`
- Required: No, default: `false`

### meta.creditNotation

An option that requires displaying the credit of this model.

When the property is `required`, users of the model must show the credit of the model.
When the property is `unnecessary`, users of the model do not have to show the credit of the model.

- Type: `string`
- Required: No, default: `required`
- Allowed values:
  - `required`
  - `unnecessary`

### meta.allowRedistribution

A flag that permits redistribution of this model.

- Type: `boolean`
- Required: No, default: `false`

### meta.modification

An option that gives permission to modify the model, or redistribute the modified model.

When the property is `prohibited`, users cannot modify the model.
When the property is either `allowModification` or `allowModificationRedistribution`, users can modify the model.
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
