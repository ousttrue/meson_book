# compile options

## include

https://mesonbuild.com/Include-directories.html

```meson.build
executable('hell', 'main.cpp',
  include_directories: include_directories('.'),
)
```

:::warning

デフォルトでカレントフォルダが `-I` に追加される。
システムのヘッダと同名のファイルがローカルに存在した場合に、意味不明な大量のエラーが出る可能性がある。
`stdint.h` がシャドウされるなど変なことが起きうる。

https://mesonbuild.com/Reference-manual_functions.html#library_implicit_include_directories

必要に応じて `implicit_include_directories: false` を明示する。
:::

## cflags

https://mesonbuild.com/Adding-arguments.html

### global

```meson.build
add_project_arguments('-DMYPROJ=projname', language : 'c')


add_project_arguments(
    '-DNOMINMAX',
    '-D_USE_MATH_DEFINES',
    language: 'cpp',
)
```

### target

```meson.build
c_args: ['-DNOMINMAX'],
cpp_args: ['-D_USE_MATH_DEFINES'],
```

