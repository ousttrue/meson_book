# dependency を作る

## declare\_dependency

```meson.build
project('some', 'cpp')
some_lib = library('some', ['some.cpp'])
some_dep = declare_dependency(
    link_with some_lib
)
```

以下のように `project` が namespace になる。

* some(project)
  * some\_lib(build target)
  * some\_dep(dependency)

## subprojects に展開する

Intel TBB を展開する例。

```
project('tbb', 'cpp')

cc = meson.get_compiler('cpp')

if get_option(
    'buildtype',
).startswith('debug') # debug or debugoptimized
    tbb_lib = cc.find_library(
        'tbb_debug',
        dirs: meson.current_source_dir() + '/lib/intel64/vc14',
    )
else
    tbb_lib = cc.find_library(
        'tbb',
        dirs: meson.current_source_dir() + '/lib/intel64/vc14',
    )
endif

tbb_dep = declare_dependency(
    include_directories: include_directories('include'),
    dependencies: tbb_lib,
)
meson.override_dependency('tbb', tbb_dep)
```

使用するには、

```
tbb_dep = subproject('tbb').get_variable('tbb_dep')
# もしくは
tbb_dep = dependency('tbb') # meson.override_dependency が対応する
```

