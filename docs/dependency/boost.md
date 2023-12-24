# boost

header only の場合

アーカイブを展開して環境変数 `BOOST_ROOT` を指定する。
ビルドが必要な部分は試していない。

```
# 例。D:\boost\boost_1_82_0 に解凍した
BOOST_ROOT=D:\boost\boost_1_82_0
```

```
boost_dep = dependency('boost')
```

とする。

https://mesonbuild.com/Dependencies.html#boost


