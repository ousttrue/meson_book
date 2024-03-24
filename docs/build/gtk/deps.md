# gtk ビルドの準備

:::info prefix を決める

install 先の folder を決めます。

`C:\gnome` や `%USERPROFILE%\local` など。

以降、`$PREFIX` などと記述します。
:::

作業は powershell で進めます。

- Windows11(64bit)
- vc build tool 2022 https://visualstudio.microsoft.com/ja/visual-cpp-build-tools/
- 最新版の python3 (python-3.12 だった)
- vulkan sdk(gtk4 のビルドで参照があった)

## tools

### meson

```sh
$ pip install meson
# => C:/Python312/Scripts/meson.exe
```

### cmake & ninja

```sh
$ pip install ninja cmake
```

### win_flex

https://github.com/lexxmark/winflexbison/releases

パスを通す

### gperf

https://gnuwin32.sourceforge.net/packages/gperf.htm

パスを通す
