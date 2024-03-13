---
title: MesonBook
author: ousttrue
slide: false
---

[Manual](https://mesonbuild.com/Manual.html)

- https://github.com/mesonbuild/meson
- [Release notes](https://mesonbuild.com/Release-notes.html)

## 更新メモ

- 2025-03 更新(write, build, vala)
- 2024-01-08 更新
- 2023-12-25 Qitta から移植

## install

meson は python 製のコマンドラインツールなので、新しいめの python-3 が必要です。
python モジュールとして pip でインストールします。

https://pypi.org/project/meson/

```sh
> pip install meson
```

:::note
Windows では `C:/Python310/Scripts/meson.exe` にインストールされます。
:::

:::note
ninja も pip でいける

```sh
> pip install ninja
```

cmake もいける。

```sh
> pip install cmake
```

zig もいける。

```sh
> pip install ziglang
```

:::

## 参考

### 2022

[Meson の使い方メモ - はしくれエンジニアもどきのメモ](https://cartman0.hatenablog.com/entry/2022/03/24/Meson%E3%81%AE%E4%BD%BF%E3%81%84%E6%96%B9%E3%83%A1%E3%83%A2)

[Meson を使って GObject Introspection 対応のビルドシステムを構築する方法 - 2022-08-17 - ククログ](https://www.clear-code.com/blog/2022/8/17/meson-and-gobject-introspection.html)

### 2018

[Meson ビルドシステムを使う - kakurasan](https://kakurasan.blogspot.com/2018/09/use-meson-buildsystem.html)

[CMake の代替 (となってほしい)、Meson チュートリアル #C++ - Qiita](https://qiita.com/turenar/items/c727834fbf701beb47ef)

### 2016

[Hello Library World: The Meson Build System](http://hellolibraryworld.blogspot.com/2016/12/the-meson-build-system.html)
