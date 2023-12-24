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

