# basic

https://mesonbuild.com/Running-Meson.html

https://mesonbuild.com/Meson-sample.html

https://mesonbuild.com/Tutorial.html

https://mesonbuild.com/IndepthTutorial.html

https://mesonbuild.com/GuiTutorial.html

https://mesonbuild.com/howtox.html

https://mesonbuild.com/FAQ.html

ファイル構成

- main.cpp
- meson.build

```meson.build
project('tutorial', 'cpp')

demo_inc = include_directories('include')
executable('demo', 'main.cpp',
include_directories: demo_inc)
```

```sh
> meson setup buildir
> meson compile -C builddir
```

もしくは

```sh
> meson setup buildir --prefix FULL_PATH_TO_INSTALL
> meson install -C builddir
```

:::note 

install までやれば exe と dll が `PREFIX/bin` に集まるので扱いやすい場合がある。
Windows では exe と同じパスからも dll がサーチされる。

:::

## 命名規則(慣習)

https://mesonbuild.com/Style-guide.html

:::note 
変数名に _inc, _lib, _dep を付けて型を表します。

例えば hoge という project の場合、

```meson.build
project('hoge', ['c', 'cpp'])
hoge_inc = include_directories('include')
hoge_lib = library('hoge', [
  'src/hoge.cpp',
],
  include_directories: hoge_inc,
)
hoge_dep = declare_dependency(
  include_directories: hoge_inc,
  link_with: hoge_lib,
)
```

```meson.build
hoge_dep = dependency('hoge') # dep として参照される
# もしくは
hoge = subproject('hoge') # subproject として参照される
hoge_dep = hoge.get_variable('hoge_dep') # 変数として取り出し

executable('fuga', ['main.cpp'],
  dependencies: [hoge_dep],
)
```

:::

## meson setup の状態を確認する

https://mesonbuild.com/Configuring-a-build-directory.html

```sh
> meson configure builddir
```

## .gitignore

```.gitignore
/.cache/
/build/
/builddir/
/subprojects/*
!/subprojects/packagefiles
!/subprojects/some.wrap # 👈 *.wrap でだいたい OK だが、wrap-reidrect をコミットすると動かなくなる場合あり。依存の依存を浅い階層にコピーしている？
```

