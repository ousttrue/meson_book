# library()

```meson.build
some_lib = library('some', [ # default_library により shared か static が決まる。デフォルトは static
    'some.cpp',
],
)
```

## static_library

## shared_library

## both_libraries

static と shared の両方をビルドさせる
