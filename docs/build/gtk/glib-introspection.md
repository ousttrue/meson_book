# glib-introspection

https://github.com/GNOME/gobject-introspection

```sh
$ meson setup builddir --prefix "$HOME/local" -Dbuildtype=release
$ meson install -C builddir
```

## g-ir-scanner

MSVC版のビルドでは最大の難所でした。
g-ir-scanner が動作するように
環境変数 GI_EXTRA_BASE_DLL_DIRS が必要です。

```sh
> ls .\local\lib\gobject-introspection\giscanner\
__init__.py                     annotationparser.py  doctemplates    introspectablepass.py  scannermain.py    utils.py
__pycache__                     ast.py               docwriter.py    maintransformer.py     sectionparser.py  xmlwriter.py
_giscanner.cp312-win_amd64.lib  cachestore.py        dumper.py       mdextensions.py        shlibs.py
_giscanner.cp312-win_amd64.pyd  ccompiler.py         gdumpparser.py  message.py             sourcescanner.py
_version.py                     codegen.py           girparser.py    msvccompiler.py        testcodegen.py
annotationmain.py               docmain.py           girwriter.py    pkgconfig.py           transformer.py
```
