# toolchain

https://mesonbuild.com/Machine-files.html

https://mesonbuild.com/Reference-tables.html

setup 時に引数 `--native-file NATIVE_FILE` もしくは `--cross-file CROSS_FILE` を使うことで
違うコンパイラを選択できる。
NATIVE_FILE と CROSS_FILE の内容はだいたい同じで、フォーマットは `ini` ファイル。
コンパイラに clang や mingw 明示したいときに `--native-file` を使い、
さらに違うプラットフォーム向けにビルドするときに `--cross-file` を使う。
`native` と　`cross` をあべこべにしても動くかもしれないがよくわからない。 
変更を反映するには、 `meson setup` のやりなおしが必要。

[Mesonの使い方メモ - はしくれエンジニアもどきのメモ](https://cartman0.hatenablog.com/entry/2022/03/24/Meson%E3%81%AE%E4%BD%BF%E3%81%84%E6%96%B9%E3%83%A1%E3%83%A2)

[Cross and Native File reference](https://mesonbuild.com/Machine-files.html#cross-and-native-file-reference)

