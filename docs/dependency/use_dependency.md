# dependency を使う

以下の記述をする。

```meson title="meson.build"
freetype2_dep = dependency('freetype2')
```

```meson title="meson.build"
# default_options は、project の default_options, meson_options.txt などの変更ができる
some_dep = dependency('some', deafult_options: ['default_library=static'])

# 👇
# dependency は subproject に対して get_variable するのと同等
fmt_dep = subproject('fmt', default_options: ['default_library=static']).get_variable('fmt_dep')
```

https://mesonbuild.com/Reference-manual\_functions.html#dependency

この記述により、`dependency関数` は `dep` 型のオブジェクトを返す。
dep 型なので `_dep` と名付ける慣習。
dependency 関数は、一定の順番で依存対象をシステムからサーチする。
`pkg-config` などが使われます。
もしシステムから依存対象を発見できなかったら `subprojects` フォルダにフォールバックします。

:::note

Unix系では、`pkg-config` で見つかることが多くて、Windows では `subprojects` を
使うことが多くなると思われる。

:::

## dependency名？

`dependency()` は project 名でなくて、`dependency名?` で解決されるポイ。
project の中に複数の `dependency名` を内包することができる。

dependency名は以下のいずれかの方法で宣言できる。

```meson title="subprojects/ogg/meson.build"
meson.override_dependency('ogg', ogg_dep) # 👈 ogg が dependency名
```

もしくは、

```meson title="subprojects/ogg.wrap"
[provide]
ogg = ogg_dep # 👈 ogg が dependency名
```

## dependency の伝搬

```meson
some_inc = include_drectories(.)
some_lib = library('some', [],
include_drectories: some_inc # 👈 伝搬しない(CMake の target_include_directories PRIVATE)
some_dep = declare_dependency(
    include_derectories: some_inc, # 👈 伝搬する(CMake の target_include_directories PUBLIC)
)
```

## subproject の dependency 参照

- some(project)
  - some_lib(build target)
  - some_dep(dependency)

```meson.build
some_dep = subproject('some').get_variable('some_dep')
```

## wrap redirect

ネストした subprojects の wrap ファイルが上位階層にコピーされる。
これは `.gitignore` する必要がある。

```subprojects\expat.wrap
[wrap-redirect]
filename = fontconfig-2.14.1\subprojects\expat.wrap
```


