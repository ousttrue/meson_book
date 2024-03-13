# GTK 関連のビルド

:::note

- [Windows上の Python-3.11 で動く Gtk4 や GStreamer をビルドする](https://qiita.com/ousttrue/items/ac591be1654615e1b178)

が最新版だとうまくビルドできなかったのでリライトします。

:::

GTK 周辺のプロジェクトは meson で記述されているものが多いです。
GTK を中心に、MSVC 環境でビルドする方法をまとめます。

最初に gobject-introspection の確保を目指します。
gobject システムの依存のねっこになります。
パッケージ間の循環参照が解消されて、
以降は基本的に足していくだけになります。

## source

[GNOME Github Mirror](https://github.com/GNOME)
が便利です。
git じゃなくて archive は、 https://download.gnome.org/sources/
にまとまっています。
