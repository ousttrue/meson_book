# link options

```meson title="meson.build"
# winmm.lib にリンクする
c_compiler = meson.get_compiler('c')
winmm_dep = c_compiler.find_library('winmm', required: true)
executable('hello', [
  'main.cpp'
],
  dependencies: [winmm_dep],
)
```

# lib dir ?

