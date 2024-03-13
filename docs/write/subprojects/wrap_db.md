# wrap db

https://mesonbuild.com/Wrapdb-projects.html

https://mesonbuild.com/Adding-new-projects-to-wrapdb.html

* wrap ファイルをダウンロードして、`subprojects/some.wrap` に配置

:::note

コマンドラインでできる。

```
meson wrap install sdl2
```

:::

https://mesonbuild.com/Using-wraptool.html

他にもいろいろコマンドあり。

```
meson wrap list
```

* meson.build に `some_dep = dependency('some')` と記述する
* target に `dependencies: [some_dep]` と記述する

