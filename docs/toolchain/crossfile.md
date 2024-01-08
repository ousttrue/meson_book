# `--cross-file`

https://mesonbuild.com/Cross-compilation.html

## emscripten

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
