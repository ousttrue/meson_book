# library()

```meson.build
some_lib = library('some', [ # default_library により shared か static が決まる。デフォルトは static
    'some.cpp',
],
)
```

## static_library

## shared_library

```meson.build title="def file"
shared_library(
    'RecipeThumbnailProvider',
    [
        'Dll.cpp',
        'RecipeThumbnailProvider.cpp',
    ],
    vs_module_defs: 'RecipeThumbnailProvider.def', #  👈
    install: true,
)
```

## both_libraries

static と shared の両方をビルドさせる
