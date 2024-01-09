# dependency

https://mesonbuild.com/Dependencies.html

meson は project 内に変数として target などを持つ。
subproject から get_variable で変数(subproject, dep, target) などを得られる。

https://mesonbuild.com/Reference-manual_returned_subproject.html

target は dep に依存する。dep の中に別の target がある。

https://mesonbuild.com/Reference-manual_returned_dep.html

https://qiita.com/ousttrue/items/e13a55d9b8733f2a4457

## dependency 解決は２段構え

`dependency('hoge')` で依存を記述する。
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


## システムライブラリ(pkg-configなど)を無視して subprojects を使うには？

```meson
# dependency 関数ではなく、subproject 関数を使う。
dep = subproject('some').get_variable('some_dep')
```

## dependency の include 警告が出ないようにする

```meson
directxmath_dep = dependency('directxmath', include_type: 'system')
```

https://github.com/mesonbuild/meson/issues/963

