# executable()

```
executable('hello', [
    'main.cpp'
],
)
```

## winmain

https://mesonbuild.com/Reference-manual_functions.html#executable_win_subsystem

```
executable('hello', [
    'main.cpp'
],
    win_subsystem: 'windows',
)
```

:::note
`gui_app: true` は deprecated
:::

## rc: windows resource

```
windows = import('windows')
executable('app', [
    windows.compile_resources('app.rc',
      include_directories: wtl_inc), # 必要に応じて
    main.cpp
])
```
