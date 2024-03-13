# project

project root の meson.build の先頭に記述する。
プロジェクト名、使う言語(c, cpp...)が必須。
言語は、`c`, `cpp`, `['c', 'cpp']` のように記述する。

その他に、コンパイラの全体設定(c++20)や version 記述などができる。

```meson title="meson.build"
project('hello', ['c', 'cpp'],
version: '1.0.0',
default_options: [
    'cpp_std=c++20',
])
```

https://mesonbuild.com/Reference-manual_functions.html#project

https://mesonbuild.com/Reference-manual_returned_subproject.html

## default_options

project 全体の設定を変更する。

https://mesonbuild.com/Builtin-options.html

:::note warn
project の default_options 変更時は `meson setup --wipe` が必要

`ninja`, `meson compile` するだけでは反映されないので注意。
また `meson setup --reconfigure` ではだめ。
:::

### default_library
static と shared を選択できる。デフォルトは shared ?

```meson title="meson.build"
project(
default_options: ['default_library=static']
)
```

### cpp_std

```meson title="meson.build"
project(
default_options: ['cpp_std=c++17']
)
```

## 条件式

```meson
if host_machine.system() == 'windows'
endif
```


