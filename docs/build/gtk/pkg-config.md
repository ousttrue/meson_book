# pkg-config コマンド

https://www.freedesktop.org/wiki/Software/pkg-config/

:::warning

pkg-config は glib に依存しています。

:::

:::info
環境変数 PKG_CONFIG_PATH を設定。場所は prefix + lib\pkgconfig
:::

```sh
$ nmake /f Makefile.vc CFG=release
```

## 他の pkg-config 実装

:::warning pkg-config lite

https://sourceforge.net/projects/pkgconfiglite/

`g-ir-scanner` で使われる
pkg-config の vc 向けオプションがうまく動かなかった。

:::

:::note

https://github.com/pkgconf/pkgconf

まだ試していない。
こっちの方が依存が少なく meson.build で楽。

:::
