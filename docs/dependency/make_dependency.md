# dependency を作る

## declare_dependency

https://mesonbuild.com/Reference-manual_functions.html#declare_dependency

```meson.build title="とりあえず include を渡す例"
some_dep = declare_dependency( # 👈
    include_directories: include_directories('.'),
)
```

```meson.build title="link も含む例"
some_lib = library('some', ['some.cpp'])
some_dep = declare_dependency( # 👈
    include_directories: include_directories('.'),
    link_with: some_lib,
    compile_args: ['-DNOMINMAX'], # target の cpp_args, c_args 両用
)
```

## subprojects に展開する

subproject ディレクトリに他のプロジェクトを展開して参照できる。
Intel TBB を展開する例。

```meson.build title="subproject/tbb/meson.build"
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
