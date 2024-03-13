# gtk ビルドの準備

:::info prefix を決める

install 先の folder を決めます。

`C:\gnome` や `%USERPROFILE%\local` など。

以降、`$PREFIX` などと記述します。
:::

- Windows11(64bit)
- vc build tool 2022 https://visualstudio.microsoft.com/ja/visual-cpp-build-tools/
- 最新版の python3 (python-3.12 だった)
- pip install meson => C:/Python312/Scripts/meson.exe
- pip install ninja cmake
- win_flex win_flex_bison3-latest.zip パス通す
- vulkan sdk

pkg-config lite パス通す
環境変数 PKG_CONFIG_PATH を設定。場所は prefix + lib\pkgconfig

作業は powershell で進めます。

