# shader から header を作る

https://mesonbuild.com/Generating-sources.html

```meson.build
kernels = [
    'hlslComputeKernel.hlsl',
    'hlslPatchCommon.hlsl',
    'hlslPatchCommonTess.hlsl',
    'hlslPatchBoxSplineTriangle.hlsl',
    'hlslPatchBSpline.hlsl',
    'hlslPatchGregory.hlsl',
    'hlslPatchGregoryBasis.hlsl',
    'hlslPatchGregoryTriangle.hlsl',
    'hlslPatchLegacy.hlsl',
    #
    'patchBasis.h',
    'patchBasisTypes.h',
]
gens = []
foreach k : kernels
    sp = k.split('.')
    g = custom_target(
        sp[0] + '_gen_h',
        output: sp[0] + '.gen.h',
        input: k,
        command: [osd_stringify, '@INPUT@', '@OUTPUT@'],
    )
    gens += g
endforeach
```

