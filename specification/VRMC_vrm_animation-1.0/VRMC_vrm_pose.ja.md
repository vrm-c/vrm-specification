# VRMC_vrm_pose

## Dependencies

glTF 2.0仕様に向けて策定されています。

また、 [`VRMC_vrm_animation`](README.ja.md) に依存します。

## Overview

`VRMC_vrm_pose` 仕様は、VRMC_vrm_animation に対する拡張です。
`VRMC_vrm_animation` がアニメーションを格納するのに対して、`VRMC_vrm_pose` は1フレームの `ポーズ` を格納します。
`VRMC_vrm_animation` がアニメーションを `/animations` に格納して `/accessors` 経由でバイナリデータを参照をするのに対して、
`VRMC_vrm_pose` は、`/extensions/VRMC_vrm_animation/extensions/VRMC_vrm_pose` 下に JSON テキストでデータを保持します。

## glTF Schema Updates

`VRMC_vrm_pose` 拡張はglTFの `VRMC_vrm_animation` 拡張の拡張として定義されます。

```json
{
  "nodes": [
    // ...
  ],
  "extensions": {
    "VRMC_vrm_animation": {
      "specVersion": "1.0-draft",
      "humanoid": {
        "humanBones": {
          "hips": { "node": 0 },
          "spine": { "node": 1 },
          "chest": { "node": 2 },
          // ...
        }
      },
      "extensions": {
        "VMRC_vrm_pose": {
          "humanoid": {
            "translation": [0, 1, 0],
            "rotations": {
              "hips": [0, 0.7, 0, 0.7],
              "spine": [0, 0.7, 0, 0.7],
              // ...
            }
          },
          "expressions": {
            "preset": {
              "happy": 1.0,
            },
          },
          "lookAt": {
            "position": [4, 5, 6],
          },
        }
      }
    }
  },
  // ...
}
```
