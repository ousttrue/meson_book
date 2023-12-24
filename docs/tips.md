# tips

## c++2b など新しいフラグに未対応

- `1.0`
- `1.1rc2`

未対応。

https://github.com/mesonbuild/meson/pull/9053

`.local/lib/python3.10/site-packages/mesonbuild/compilers/cpp.py`

の該当個所に 'c++2b' を追加してしまった。
gcc-12 で `std::expected` 動いた。

## meson + msvc on github-action

`vcvars64.bat` 的な対策

https://github.com/bus1/cabuild

## zig cc

https://zenn.dev/ousttrue/books/b2ec4e93bdc5c4/viewer/abcc8a


