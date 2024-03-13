# c++2b など新しいフラグに未対応

- `1.0`
- `1.1rc2`

未対応。

https://github.com/mesonbuild/meson/pull/9053

`.local/lib/python3.10/site-packages/mesonbuild/compilers/cpp.py`

の該当個所に `c++2b` を追加してしまった。
gcc-12 で `std::expected` が動いた。

