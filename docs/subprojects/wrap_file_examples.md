# wrap file 例

`subprojects/{name}.wrap` と `subprojects/packagefiles/{name}/meson.build` の２つを作ることが多い。

## stb(header only)

```config:subprojects/stb.wrap
[wrap-git]
url = https://github.com/nothings/stb
revision = master
depth = 1
patch_directory = stb

[provide]
stb = stb_dep
```

```meson:subprojects/packagefiles/stb/meson.build
project('stb')
stb_dep = declare_dependency(
    include_directories: include_directories('.'),
)
```

## plog(header only)

```config:subprojects/plog.wrap
[wrap-git]
url = https://github.com/SergiusTheBest/plog
revision = master
depth = 1
patch_directory = plog

[provide]
plog = plog_dep
```

```meson:subprojects/packagefiles/plog/meson.build
project('plog')
plog_inc = include_directories('include')
plog_dep = declare_dependency(
    include_directories: plog_inc,
)
```

