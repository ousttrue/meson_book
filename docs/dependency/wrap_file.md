# wrap file

`subprojects/some.wrap` のように配置する。

https://mesonbuild.com/Wrap-dependency-system-manual.html#wrap-format

下記の `[provide]` が必須で左辺が、 `dependency('ogg')` のように名前を決めている。

```
[provide]
ogg = ogg_dep
```

* wrap-file, wrap-git などしたり直接 subprojects にファイルを配置したりできる。
* subprojects/hoge.wrap と subprojects/packagefiles/hoge/meson.build がペアになる。

https://mesonbuild.com/Wrap-dependency-system-manual.html

## wrap-file

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

## wrap-git

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

## patch\_directory

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

## diff\_files

meson setup 時に wrap file により `subprojects/some` フォルダが作成されるときに、一度だけ diff\_files が適用される。

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

```sh title="git diff"
subprojects/hoge$ git diff > ../packagefiles/hoge.patch
```

```subprojects/hoge.wrap
diff_files = hoge.patch # subprojects/packagefiles からの相対パス
```


