## glib 二回め

`gir の生成`

```sh
$ meson setup builddir --prefix "$HOME/local" -Dbuildtype=release -Dintrospection=enabled --reconfigure
```

:::danger error!

```
FAILED: girepository/introspection/GLib-2.0.gir
```

`g-ir-scanner` の実行に失敗します。

```python
ModuleNotFoundError: No module named '_giscanner'
```

`g-ir-scanner` は python で native module _giscanner の import に失敗します。

`ModuleNotFoundError` は _giscanner.pyd が見つかるが import できないエラーです。

環境変数 GI_EXTRA_BASE_DLL_DIRS を、_giscanner.pyd が参照する dll のあるパスに向けます。

```sh title="powershell の例"
$env:GI_EXTRA_BASE_DLL_DIRS="$HOME\local\bin"
```

:::

```sh
> ls .\local\share\gir-1.0\
DBus-1.0.gir          GL-1.0.gir         GObject-2.0.gir   cairo-1.0.gir       libxml2-2.0.gir  xlib-2.0.gir
DBusGLib-1.0.gir      GLib-2.0.gir       Gio-2.0.gir       fontconfig-2.0.gir  win32-1.0.gir    xrandr-1.3.gir
GIRepository-2.0.gir  GLibWin32-2.0.gir  GioWin32-2.0.gir  freetype2-2.0.gir   xfixes-4.0.gir
GIRepository-3.0.gir  GModule-2.0.gir    Vulkan-1.0.gir    gir-1.2.rnc         xft-2.0.gir
```

