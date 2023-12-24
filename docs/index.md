---
title: MesonBook
author: ousttrue
slide: false
---

https://mesonbuild.com/

- https://github.com/mesonbuild/meson
- https://mesonbuild.com/Release-notes.html

msvc 版の gstreamer が一挙にビルドできてしまうなど、強力なビルドツールです。
ローカルディレクトリーで依存もビルドする使いかたに向いているので、
意外とWindows(MSVC) と相性が良いです。

## 更新メモ

- 2023-12-25 Qitta から移植

## install

meson は python で記述されているので、新しいめの python-3 が必要です。
python モジュールとして pip でインストールします。

https://pypi.org/project/meson/

```
pip install meson
```

:::note 
ninja も pip でいける

```
pip install ninja
```

- cmake もいける。
- zig もいける。
:::

## 参考

https://mesonbuild.com/Users.html

https://qiita.com/ousttrue/items/ac591be1654615e1b178

### 2022

https://cartman0.hatenablog.com/entry/2022/03/24/Meson%E3%81%AE%E4%BD%BF%E3%81%84%E6%96%B9%E3%83%A1%E3%83%A2

https://www.clear-code.com/blog/2022/8/17/meson-and-gobject-introspection.html

### 2018

https://kakurasan.blogspot.com/2018/09/use-meson-buildsystem.html

https://qiita.com/turenar/items/c727834fbf701beb47ef

### 2016

http://hellolibraryworld.blogspot.com/2016/12/the-meson-build-system.html

