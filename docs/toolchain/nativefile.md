# `--native-file`

meson デフォルトとは別のコンパイラを使う

## clang-19(windows)

- https://github.com/mstorsjo/llvm-mingw を使う
- https://github.com/GNOME/glib をビルドできた

```ini
[constants]
toolchain = 'E:/llvm-mingw-20241203-ucrt-x86_64'

[binaries]
c= toolchain / 'bin/clang.exe'
cpp= toolchain / 'bin/clang++.exe'
c_ld = toolchain / 'bin/ld.lld.exe'
cpp_ld = toolchain / 'bin/ld.lld.exe'
ar = toolchain / 'bin/ar.exe'
windres = toolchain / 'bin/windres.exe'
```

## clang-16 と libc++(Ubuntu-22.04)

- std::expected を使えた

```ini
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
