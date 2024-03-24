# glib

```text title="循環する参照"
glib <- gobject-introspectio
 |       ^
 |       |
 +-------+glib(のgir)
```

:::tip

`-Dintrospection=disabled` を付けてビルドします。

:::

```sh title="powershellの例"
$ meson setup builddir --prefix $PREFIX -Dbuildtype=release -Dintrospection=disabled

 Subprojects
    gvdb               : YES
    libffi             : YES 1 warnings
    pcre2              : YES 1 warnings
    proxy-libintl      : YES 2 warnings
    zlib               : YES

  User defined options
    buildtype          : release
    prefix             : $PREFIX
    introspection      : disabled

$ meson install -C builddir
```
