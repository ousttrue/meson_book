# basic

- https://mesonbuild.com/Running-Meson.html
- https://mesonbuild.com/Meson-sample.html
- https://mesonbuild.com/Tutorial.html
- https://mesonbuild.com/IndepthTutorial.html
- https://mesonbuild.com/GuiTutorial.html
- https://mesonbuild.com/howtox.html
- https://mesonbuild.com/FAQ.html

ファイル構成

- main.cpp
- meson.build

```meson.build title="meson.build"
project('tutorial', 'cpp')

demo_inc = include_directories('include')
executable('demo',
  ['main.cpp'],
  include_directories: demo_inc
)
```

```sh
> meson setup builddir
> meson compile -C builddir
```

もしくは

```sh
> meson setup builddir --prefix FULL_PATH_TO_INSTALL
> meson install -C builddir
```

:::note

install までやれば exe と dll が `PREFIX/bin` に集まるので扱いやすい場合がある。
Windows では exe と同じパスからも dll がサーチされる。

:::

## 変数の命名規則(慣習)

https://mesonbuild.com/Style-guide.html

:::note
変数名に \_inc, \_lib, \_dep を付けて型を表します。

例えば hoge という project の場合、

```meson.build title="meson.build"
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
/subprojects/* # 👈
!/subprojects/packagefiles # 👈 部分的に除外する
!/subprojects/some.wrap # 👈 *.wrap でだいたい OK だが、meson 実行時に wrap-reidrect が生成される場合があり、これはコミットしたくない。
```
