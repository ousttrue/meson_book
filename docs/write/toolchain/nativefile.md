# `--native-file`

meson がデフォルトで使うコンパイラ以外を使う。

:::note info
'cc', 'gcc', 'g++', 'cl.exe' などをパスから探してデフォルトの
コンパイラを来めているようです。
:::

## clang-16 と libc++

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
