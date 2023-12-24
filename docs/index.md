---
title: MesonBook
author: ousttrue
slide: false
---

https://mesonbuild.com/

機能は cmake よりいいと思うのだけど、必要な情報がドキュメントのどこに書いてあるか見つけづらいのでここに整理する。

https://github.com/mesonbuild/meson

最近バージョンが `1.0.0` になってた。

https://mesonbuild.com/Release-notes.html

# install

```
pip install meson
```

https://pypi.org/project/meson/

:::note 
ninja も pip でいける

```
pip install ninja
```
:::

## meson は Python3 に依存する

https://mesonbuild.com/Use-of-Python.html

`pip install meson`

必須

* compiler
* Ninja

`pip install ninja` でも OK

## dependency 解決は２段構え

`dependency('hoge')` もしくは `subproject('hoge')` で依存を記述する。
まず `System` を探索し、無ければ `Subprojects` にフォールバックする。

### System ライブラリーの探索

`/usr` や `WindowsKits` からの探索。

* pkg-config
* cmake

などが使われるようだ。
また、`gl`, `boost` など探索方法がハードコーディングされたライブラリもある。

### Subproject へのフォールバック

`project_root/subprojects` フォルダの内容を使う。

* `project_root/subprojects/zlib/meson.build` のようなソースコードをフォルダに展開して meson.build を配置したもの
* `project_root/subprojects/zlib.wrap` のようなソースコードのダウンロード手順を示したもの
 
# 基本

https://mesonbuild.com/Tutorial.html

https://mesonbuild.com/IndepthTutorial.html

https://mesonbuild.com/GuiTutorial.html

https://mesonbuild.com/howtox.html

https://mesonbuild.com/FAQ.html

```meson.build
project('tutorial', 'cpp')

demo_inc = include_directories('include')
executable('demo', 'main.cpp',
include_directories: demo_inc)
```

```
meson setup buildir
ninja -C builddir
```

もしくは

```
meson setup buildir
meson compile -C builddir # 直接 ninja するのとの違いは未確認。meson.build, subproject などの meson の変更を反映する能力が違うかもしれない
```

もしくは

```
meson setup buildir --prefix FULL_PATH_TO_INSTALL
meson install -C builddir
```

install までやれば exe と dll が `PREFIX/bin` に集まるので扱いやすい場合がある。

## 慣習

https://mesonbuild.com/Style-guide.html

:::note 
変数名に _inc, _lib, _dep を付けて型を表す

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

```
meson configure builddir
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

# commandline

## setup
configure 的。

```
meson setup builddir --prefix prefix_full_path
```

reconfigure

```
meson setup builddir --prefix prefix_full_path --reconfigure
```

```
meson setup builddir --prefix prefix_full_path --wipe
```

## build

```
meson compile -C buildir
```

`-C buildir` を省略するとカレントディレクトリを使う。

## install

```
meson install -C builddir
```

`-C buildir` を省略するとカレントディレクトリを使う。

## vscode 例

```.vscode/tasks.json
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "configure",
            "type": "shell",
            "command": "meson setup builddir --prefix ${workspaceFolder}/prefix",
            "problemMatcher": [],
        },
        {
            "label": "configure(wipe)",
            "type": "shell",
            "command": "meson setup --wipe builddir --prefix ${workspaceFolder}/prefix",
            "problemMatcher": [],
        },
        {
            "label": "build",
            "type": "shell",
            "command": "meson install -C builddir",
            "problemMatcher": {
                "owner": "meson-msvc",
                "fileLocation": [
                    "autoDetect", // 👈 absolute と relative 両方
                    "${workspaceFolder}/builddir",
                ],
                "pattern": {
                    "kind": "location",
                    "regexp": "^(.*)\\((\\d+)\\): (fatal error|\\w+) (\\w+): (.*)$",
                    "file": 1,
                    "line": 2,
                    "severity": 3,
                    "code": 4,
                    "message": 5,
                }
            },
        },
    ]
}
```

meson は `compile_commands.json` を自動的に生成するので、 `clangd` で参照する。

```.vscode/settings.json
{
    "clangd.arguments": [
        "--compile-commands-dir=${workspaceFolder}/builddir"
    ],
}
```

:::note
ビルドディレクトリが cmake と被らないように builddir を使う。

https://marketplace.visualstudio.com/items?itemName=mesonbuild.mesonbuild

がデフォルトで builddir を使う。
task も builddir にしておいた。
:::

# project

先頭に定義。
名前、使う言語(c, cpp...)、コンパイラの全体設定(c++20)など。

```meson.build
project('hello', ['c', 'cpp'],
version: '1.0.0',
default_options: [
    'cpp_std=c++20',
])
```

https://mesonbuild.com/Reference-manual_functions.html#project

https://mesonbuild.com/Reference-manual_returned_subproject.html

root project は暗黙裡に `this` として参照されている感じぽい？

## default_options
project 全体の設定を変更する。

https://mesonbuild.com/Builtin-options.html

:::note warn
project の default_options 変更時は `meson setup --wipe` が必要

`ninja`, `meson compile` するだけでは反映されないので注意。
また `meson setup --reconfigure` ではだめ。
:::

### default_library
static と shared を選択する。デフォルトは shared ?

```meson.build
project(
default_options: ['default_library=static']
)
```

のように指定できる。

## project init
空プロジェクトの生成

https://mesonbuild.com/Project-templates.html

## compile options
### include

:::note alert

デフォルトでカレントフォルダが `-I` に追加される。
危ない。
システムのヘッダと同名のファイルがローカルに存在した場合に、意味不明な大量のエラーが出る可能性がある。
`stdint.h` がシャドウされるなど変なことが起きうる。

https://mesonbuild.com/Reference-manual_functions.html#library_implicit_include_directories

必要に応じて `implicit_include_directories: false` を明示する。
:::

## link

```meson.build
# winmm.lib にリンクする
c_compiler = meson.get_compiler('c')
winmm_lib = c_compiler.find_library('winmm', required: true)
executable('hello', [
  'main.cpp'
],
  dependencies: [winmm_lib],
)
```

## 条件式

```meson
if host_machine.system() == 'windows'
endif
```

## config.h を生成する

```py
project(
    'App',
    'cpp',
    version: '1.4.0',
    license: 'MIT',
    default_options: [],
)

cdata = configuration_data()
cdata.set_quoted('PACKAGE', meson.project_name())
cdata.set_quoted('PACKAGE_VERSION', meson.project_version())
configure_file(
    output: 'config.h',
    configuration: cdata,
)

# build_dir に config.h は出力される
# include できるようにするには下記のようにする
# '.' は build_dir を指すぽい。
config_inc = include_directories('.')
```

生成された `config.h`

```c
/*
 * Autogenerated by the Meson build system.
 * Do not edit, your changes will be lost.
 */

#pragma once

#define PACKAGE "App"

#define PACKAGE_VERSION "1.4.0"
```

GIT の情報を埋め込む例

[meson and git information](https://app.slack.com/client/TBV0XKV40/CCJUCGTDG)

# target

https://mesonbuild.com/Reference-manual_returned_build_tgt.html

## executable

```
executable('hello', [
    'main.cpp'
],
)
```

### winmain

https://mesonbuild.com/Reference-manual_functions.html#executable_win_subsystem

```
executable('hello', [
    'main.cpp'
],
    win_subsystem: 'windows',
)
```

:::note
`gui_app: true` は deprecated
:::

### rc: windows resource

```
windows = import('windows')
executable('app', [
    windows.compile_resources('app.rc',
      include_directories: wtl_inc), # 必要に応じて
    main.cpp
])
```

## library

```
some_lib = library('some', [ # default_library により shared か static が決まる。デフォルトは static
    'some.cpp',
],
)
```

- static_library
- shared_library
- both_libraries 👈 static と shared の両方をビルドさせる

# dependency

meson は project 内に変数として target などがある。
subproject から get_variable で変数(subproject, dep, target) などが得られる。

https://mesonbuild.com/Reference-manual_returned_subproject.html

target が dep に依存する。dep の中に別の target がある。

https://mesonbuild.com/Reference-manual_returned_dep.html

https://qiita.com/ousttrue/items/e13a55d9b8733f2a4457

## dependency を使う

以下の記述をする。

```meson.build
freetype2_dep = dependency('freetype2')
```

```meson.build
# default_options は、project の default_options, meson_options.txt などの変更ができる
some_dep = dependency('some', deafult_options: ['default_library=static'])

# 👇
# dependency は subproject に対して get_variable するのと同等
fmt_dep = subproject('fmt', default_options: ['default_library=static']).get_variable('fmt_dep')
```

https://mesonbuild.com/Reference-manual_functions.html#dependency

この記述により、`dependency関数` は `dep` 型のオブジェクトを返す。
dep 型なので `_dep` と名付ける慣習。
dependency は 引数のキーに、`pkg-config` などでシステムのライブラリをサーチして、
無かったら `subprojects` フォルダにフォールバックするという動作をする。
Unix系では、`pkg-config` で見つかることが多くて、Windows では `subprojects` を
使うことが多くなると思われる。

### dependency名？

`dependency()` は project 名でなくて、`dependency名?` で解決されるポイ。
project の中に複数の `dependency名` を内包することができる。

dependency名は以下のいずれかの方法で宣言できる。

```subprojects/ogg/meson.build
meson.override_dependency('ogg', ogg_dep) # 👈 ogg が dependency名
```

もしくは、

```subprojects/ogg.wrap
[provide]
ogg = ogg_dep # 👈 ogg が dependency名
```

### dependency の伝搬

```
some_inc = include_drectories(.)
some_lib = library('some', [],
include_drectories: some_inc # 👈 伝搬しない(CMake の target_include_directories PRIVATE)
some_dep = declare_dependency(
    include_derectories: some_inc, # 👈 伝搬する(CMake の target_include_directories PUBLIC)
)
```

### wrap redirect

ネストした subprojects の wrap ファイルが上位階層にコピーされる。
これは `.gitignore` する必要がある。

```subprojects\expat.wrap
[wrap-redirect]
filename = fontconfig-2.14.1\subprojects\expat.wrap
```

### システムライブラリ(pkg-configなど)を無視して subprojects を使うには？

TODO:

### dependency の include 警告が出ないようにする

```
directxmath_dep = dependency('directxmath', include_type: 'system')
```

https://github.com/mesonbuild/meson/issues/963

## dependency を作る

### declare_dependency

```meson.build
project('some', 'cpp')
some_lib = library('some', ['some.cpp'])
some_dep = declare_dependency(
    link_with some_lib
)
```

以下のように `project` が namespace になる。

- some(project)
  - some_lib(build target)
  - some_dep(dependency)

### subprojects に展開する

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

### wrap file
`subprojects/some.wrap` のように配置する。

https://mesonbuild.com/Wrap-dependency-system-manual.html#wrap-format

下記の `[provide]` が必須で左辺が、 `dependency('ogg')` のように名前を決めている。

```
[provide]
ogg = ogg_dep
```

- wrap-file, wrap-git などしたり直接 subprojects にファイルを配置したりできる。
- subprojects/hoge.wrap と subprojects/packagefiles/hoge/meson.build がペアになる。

https://mesonbuild.com/Wrap-dependency-system-manual.html

#### wrap-file

zip や tar.gz を download する

```subprojects/openxr_loader.wrap
[wrap-file]
directory = openxr_loader
source_url = https://github.com/KhronosGroup/OpenXR-SDK/releases/download/release-1.0.26/OpenXR.Loader.1.0.26.nupkg
source_filename = OpenXR.Loader.1.0.26.zip
source_hash = ae874fd20e77abc2de4bc8a75ccc9a1217799a6e81cf76eee6a1cd599de45a93
patch_directory = openxr_loader
lead_directory_missing = true # 👈 展開すると bin, include, lib のようひとつの root フォルダが無い場合

[provide]
openxr_loader = openxr_loader_dep
```

```subprojects/packagefiles/openxr_loader/meson.build
project('openxr_loader', 'c')

cc = meson.get_compiler('c')
lib_dir = meson.current_source_dir() + '/native/x64/release/lib'
libs = []
libs += cc.find_library('openxr_loader', dirs: lib_dir)

openxr_loader_dep = declare_dependency(
    include_directories: include_directories('include'),
    dependencies: libs,
    compile_args: ['-DXR_USE_PLATFORM_WIN32'],
)

install_data('native/x64/release/bin/openxr_loader.dll', install_dir: 'bin')
```

#### wrap-git

git を clone する。

```subprojects/ktx.wrap
[wrap-git]
url = https://github.com/KhronosGroup/KTX-Software
revision = v4.0.0
depth = 1
patch_directory = ktx

[provide]
ktx = ktx_dep
```

```subprojects/packagefiles/ktx/meson.build
project('ktx', 'c')
ktx_inc = include_directories('include')
ktx_dep = declare_dependency(
    include_directories: ktx_inc,
)
# TODO
```

`subprojects/hoge.wrap` は `subprojects/hoge` に clone される。
`ファイル名重要`。

#### patch_directory

このフォルダのファイルを、
wrap-file や wrap-git で展開したソースディレクトリに対して上書きコピーする。

```
subprojects/
  packagefiles/
    some/ # some.wrap の `patch_directory = some` で指定する
      meson.build # 自前の meson.build
  some.wrap # wrap-file や wrap-git でソースファイルを得る
  some/ # ソースファイルのフォルダ 
      meson.build # meson setup 時に some フォルダが作成されたあとで、一度だけ package_directory/some からコピーされる。
```

```subprojects/some.wrap
patch_directory = some # packagefiles 下のフォルダ名
```

`meson.build` が無い場合、`config.h` を決め打ちにする場合などに使える。

#### diff_files

meson setup 時に wrap file により `subprojects/some` フォルダが作成されるときに、一度だけ diff_files が適用される。

:::note warn
再実行するには `subprojects/some` を消して、 `meson setup` や `meson compile` する。
:::

```
# ひとつだけファイルを修正する例
subprojects/some$ diff -up meson.build.orig meson.build > ../packagefiles/some/meson.build.patch
```

```subprojects/some.wrap
diff_files = some/meson.build.patch # subprojects/packagefiles からの相対パス
```

# git diff

```
subprojects/hoge$ git diff > ../packagefiles/hoge.patch
```

```subprojects/hoge.wrap
diff_files = hoge.patch # subprojects/packagefiles からの相対パス
```

### wrap db
https://mesonbuild.com/Wrapdb-projects.html

https://mesonbuild.com/Adding-new-projects-to-wrapdb.html

- wrap ファイルをダウンロードして、`subprojects/some.wrap` に配置

:::note

コマンドラインでできる。

```
meson wrap install sdl2
```
:::

https://mesonbuild.com/Using-wraptool.html

他にもいろいろコマンドあり。

```
meson wrap list
```

- meson.build に `some_dep = dependency('some')` と記述する
- target に `dependencies: [some_dep]` と記述する

### wrap file 例
`subprojects/{name}.wrap` と `subprojects/packagefiles/{name}/meson.build` の２つを作ることが多い。

#### stb(header only)

```config:subprojects/stb.wrap
[wrap-git]
url = https://github.com/nothings/stb
revision = master
depth = 1
patch_directory = stb

[provide]
stb = stb_dep
```

```meson:subprojects/packagefiles/stb/meson.build
project('stb')
stb_dep = declare_dependency(
    include_directories: include_directories('.'),
)
```

#### plog(header only)

```config:subprojects/plog.wrap
[wrap-git]
url = https://github.com/SergiusTheBest/plog
revision = master
depth = 1
patch_directory = plog

[provide]
plog = plog_dep
```

```meson:subprojects/packagefiles/plog/meson.build
project('plog')
plog_inc = include_directories('include')
plog_dep = declare_dependency(
    include_directories: plog_inc,
)
```

# 違うコンパイラを使う
setup 時に引数 `--native-file NATIVE_FILE` もしくは `--cross-file CROSS_FILE` を使うことで
違うコンパイラを選択できる。
NATIVE_FILE と CROSS_FILE の内容はだいたい同じで、フォーマットは `ini` ファイル。
コンパイラに clang や mingw 明示したいときに `--native-file` を使い、
さらに違うプラットフォーム向けにビルドするときに `--cross-file` を使う。
`native` と　`cross` をあべこべにしても動くかもしれないがよくわからない。 
変更を反映するには、 `meson setup` のやりなおしが必要。

https://cartman0.hatenablog.com/entry/2022/03/24/Meson%E3%81%AE%E4%BD%BF%E3%81%84%E6%96%B9%E3%83%A1%E3%83%A2

https://mesonbuild.com/Machine-files.html#cross-and-native-file-reference

## `--native-file`

### clang-16 と libc++

Ubuntu-22.04

```ini
# std::expected 使えた
[constants]
toolchain = '/usr/lib/llvm-16'

[built-in options]
cpp_args = ['-std=c++2b', '-stdlib=libc++']
cpp_link_args = cpp_args

[binaries]
c= toolchain / 'bin/clang'
cpp= toolchain / 'bin/clang++'
c_ld = toolchain / 'bin/ld.lld'
cpp_ld = toolchain / 'bin/ld.lld'
```

## `--cross-file`

https://mesonbuild.com/Cross-compilation.html

### emscripten

```
# wasm.ini
[constants]
args = []

[binaries]
c = 'emcc'
cpp = 'em++'
ar = 'emar'

[built-in options]
c_args = []
c_link_args = args
cpp_args = []
cpp_link_args = args
default_library = 'static'

[host_machine]
system = 'emscripten'
cpu_family = 'wasm'
cpu = 'wasm'
endian = 'little'
```

```
meson setup builddir --cross-file wasm.ini
```

# boost

header only の場合

アーカイブを展開して環境変数 `BOOST_ROOT` を指定する。
ビルドが必要な部分は試していない。

```
# 例。D:\boost\boost_1_82_0 に解凍した
BOOST_ROOT=D:\boost\boost_1_82_0
```

```
boost_dep = dependency('boost')
```

とする。

https://mesonbuild.com/Dependencies.html#boost

# cmake との対応

|機能|cmake|meson|備考|
|-|-|-|-|
|project|project|project|トップレベル。visual studio の sln に相当する|
|exe|add_executable|executable|
|lib|add_library|library|
|lib(static)|add_library(some STATIC)|static_library|
|lib(shared)|add_library(some SHARED)|shared_library|
|target cflags| target_compile_options | library, executable(c_args:, cpp_args:), declare_dependency(c_args:, cpp_args:) |
|target cpp std|
|target definition| target_compile_definitions | cflags と区別なし |
|target include| target_include_derectories | library, executable, declare_dependency(include_derectories:) |
|target link| target_link_libraries | library, executable(dependencies:), declare_dependency(link_with:) |
|target libdir|
|global cflags| ADD_COMPILE_OPTIONS | project(default_options: ['c_args=cflags', 'cpp_args=cxxflags']) |
|global cpp std|| project(default_options: ['cpp_std=c++20'])
|global definition| add_definitions | cflags と区別なし
|global include| add_includes |
|global link|
|global libdir|
|install||library, executable(install : true)|
|subdir|add_subdirectory, subdirs|subdir('dirname')|
|option|
|prefix|
|toolchain|
|current|CMAKE_CURRENT_LIST_DIR|meson.current_source_dir()|

# vscode

https://marketplace.visualstudio.com/items?itemName=mesonbuild.mesonbuild

この拡張でシンタックスハイライトなどが有効になる。
formatter を有効にするには、

https://muon.build/

をビルドして関連設定をする。
Windows でも `msys` ならビルドできた。
`msys` の `/usr/bin` に `muon.exe` を copy するとよさそう。

```settings.json
{
    "mesonbuild.muonPath": "C:/msys64/usr/bin/muon.exe",
    "mesonbuild.formatting.enabled": true,
    "mesonbuild.linter.muon.enabled": true,
    "mesonbuild.linting.enabled": true,
}
```

# c++2b など新しいフラグに未対応

- `1.0`
- `1.1rc2`

未対応。

https://github.com/mesonbuild/meson/pull/9053

`.local/lib/python3.10/site-packages/mesonbuild/compilers/cpp.py`

の該当個所に 'c++2b' を追加してしまった。
gcc-12 で `std::expected` 動いた。

# meson + msvc on github-action

`vcvars64.bat` 的な対策

https://github.com/bus1/cabuild

# 参考

https://mesonbuild.com/Users.html

https://qiita.com/ousttrue/items/ac591be1654615e1b178

2022

https://cartman0.hatenablog.com/entry/2022/03/24/Meson%E3%81%AE%E4%BD%BF%E3%81%84%E6%96%B9%E3%83%A1%E3%83%A2

https://www.clear-code.com/blog/2022/8/17/meson-and-gobject-introspection.html

2018

https://kakurasan.blogspot.com/2018/09/use-meson-buildsystem.html

https://qiita.com/turenar/items/c727834fbf701beb47ef

2016

http://hellolibraryworld.blogspot.com/2016/12/the-meson-build-system.html
