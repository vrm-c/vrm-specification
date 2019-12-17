
|0.0|1.0draft|new color space*1 or type|
|---|---|---|
|    _MToonVersion            |    version                                       |int->str|
|    _BlendMode               |    RenderMode                                    |int->Enum(str)|
|    _CullMode                |    cullMode                                      |int->Enum(str)|
|    renderQueue              |    renderQueueOffsetNumber *2                    |int|
|    _Color                   |    litFactor                                     |Linear|
|    _MainTex                 |    litMultiplyTexture                            |sRGB|
|    _ShadeColor              |    shadeFactor                                   |Linear|
|    _ShadeTexture            |    shadeMultiplyTexture                          |sRGB|
|    _Cutoff                  |    cutoutThresholdFactor                         |mixed decimal|
|    _ShadeShift              |    shadingShiftFactor                            |mixed decimal|
|    _ShadeToony              |    shadingToonyFactor                            |mixed decimal|
|    _ReceiveShadowRate       |    shadowReceiveMultiplierFactor                 |mixed decimal|
|    _ReceiveShadowTexture    |    shadowReceiveMultiplierMultiplyTexture        |Linear|
|    _ShadingGradeRate        |    litAndShadeMixingMultiplierFactor             |mixed decimal|
|    _ShadingGradeTexture     |    litAndShadeMixingMultiplierMultiplyTexture    |Linear|
|    _LightColorAttenuation   |    lightColorAttenuationFactor                   |mixed decimal|
|    _IndirectLightIntensity  |    giIntensityFactor                             |mixed decimal|
|    _BumpMap                 |    normalTexture                                 |Linear|
|    _BumpScale               |    normalScaleFactor                             |mixed decimal|
|    _EmissionColor           |    emissionFactor                                |Linear|
|    _EmissionMap             |    emissionMultiplyTexture                       |sRGB|
|    _SphereAdd               |    additiveTexture                               |sRGB|
|    _RimColor                |    rimFactor                                     |Linear|
|    _RimTexture              |    rimMultiplyTexture                            |sRGB|
|    _RimLightingMix          |    rimLightingMixFactor                          |mixed decimal|
|    _RimFresnelPower         |    rimFresnelPowerFactor                         |mixed decimal|
|    _RimLift                 |    rimLiftFactor                                 |mixed decimal|
|    _OutlineWidthMode        |    outlineWidthMode                              |int->Enum(str)|
|    _OutlineColor            |    outlineWidthFactor                            |Linear|
|    _OutlineWidthTexture     |    outlineWidthMultiplyTexture                   |Linear|
|    _OutlineScaledMaxDistance|    outlineScaledMaxDistanceFactor                |mixed decimal|
|    _OutlineLightingMix      |    outlineColorMode                              |int->Enum(str)|
|    _OutlineWidth            |    outlineFactor                                 |mixed decimal|
|    _OutlineLightingMix      |    outlineLightingMixFactor                      |mixed decimal|
|    _MainTex                 |    mainTextureLeftBottomOriginOffset             |_Maintex[0:2] ->2 mixed decimal|
|    _MainTex                 |    mainTextureLeftBottomOriginScale              |_Maintex[2:4] ->2 mixed decimal|
|    _UvAnimMaskTexture       |    uvAnimationMaskTexture                        |Linear|
|    _UvAnimScrollX           |    uvAnimationScrollXSpeedFactor                 |mixed decimal|
|    _UvAnimScrollY           |    uvAnimationScrollYSpeedFactor                 |mixed decimal|
|    _UvAnimRotation          |    uvAnimationRotationSpeedFactor                |mixed decimal|

 *1 (0.0 is confused by implementation then no discription)

 *2 (0.0 is value, 1.0draft is offset from basical value(undefined@2019/12/6))
