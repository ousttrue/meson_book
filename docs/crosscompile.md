# crosscompile

setup 時に引数 `--native-file NATIVE_FILE` もしくは `--cross-file CROSS_FILE` を使うことで
違うコンパイラを選択できる。
NATIVE_FILE と CROSS_FILE の内容はだいたい同じで、フォーマットは `ini` ファイル。
コンパイラに clang や mingw 明示したいときに `--native-file` を使い、
さらに違うプラットフォーム向けにビルドするときに `--cross-file` を使う。
`native` と　`cross` をあべこべにしても動くかもしれないがよくわからない。 
変更を反映するには、 `meson setup` のやりなおしが必要。

https://cartman0.hatenablog.com/entry/2022/03/24/Meson%E3%81%AE%E4%BD%BF%E3%81%84%E6%96%B9%E3%83%A1%E3%83%A2

https://mesonbuild.com/Machine-files.html#cross-and-native-file-reference

## `--native-file`

### clang-16 と libc++

Ubuntu-22.04

```ini
# std::expected 使えた
[constants]
toolchain = '/usr/lib/llvm-16'

[built-in options]
cpp_args = ['-std=c++2b', '-stdlib=libc++']
cpp_link_args = cpp_args

[binaries]
c= toolchain / 'bin/clang'
cpp= toolchain / 'bin/clang++'
c_ld = toolchain / 'bin/ld.lld'
cpp_ld = toolchain / 'bin/ld.lld'
```

## `--cross-file`

https://mesonbuild.com/Cross-compilation.html

### emscripten

```
# wasm.ini
[constants]
args = []

[binaries]
c = 'emcc'
cpp = 'em++'
ar = 'emar'

[built-in options]
c_args = []
c_link_args = args
cpp_args = []
cpp_link_args = args
default_library = 'static'

[host_machine]
system = 'emscripten'
cpu_family = 'wasm'
cpu = 'wasm'
endian = 'little'
```

```
meson setup builddir --cross-file wasm.ini
```

## zig cc

https://zenn.dev/ousttrue/books/b2ec4e93bdc5c4/viewer/abcc8a

