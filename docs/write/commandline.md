# commandline

## setup

```sh
> meson setup builddir --prefix prefix_full_path
```

reconfigure

```sh
> meson setup builddir --prefix prefix_full_path --reconfigure
```

clean

```sh
> meson setup builddir --prefix prefix_full_path --wipe
```

## build

```sh
> meson compile -C buildir
```

:::note
`-C buildir` を省略するとカレントディレクトリを使う。
:::

## install

```sh
> meson install -C builddir
```

:::note
`-C buildir` を省略するとカレントディレクトリを使う。
:::

## vscode の task 定義例

```js title=".vscode/tasks.json"
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "configure",
            "type": "shell",
            "command": "meson setup builddir --prefix ${workspaceFolder}/prefix",
            "problemMatcher": [],
        },
        {
            "label": "configure(wipe)",
            "type": "shell",
            "command": "meson setup --wipe builddir --prefix ${workspaceFolder}/prefix",
            "problemMatcher": [],
        },
        {
            "label": "build",
            "type": "shell",
            "command": "meson install -C builddir",
            "problemMatcher": {
                "owner": "meson-msvc",
                "fileLocation": [
                    "autoDetect", // 👈 absolute と relative 両方
                    "${workspaceFolder}/builddir",
                ],
                "pattern": {
                    "kind": "location",
                    "regexp": "^(.*)\\((\\d+)\\): (fatal error|\\w+) (\\w+): (.*)$",
                    "file": 1,
                    "line": 2,
                    "severity": 3,
                    "code": 4,
                    "message": 5,
                }
            },
        },
    ]
}
```

:::note
clangd の設定。

meson は `compile_commands.json` を自動的に生成するので、 `clangd` で参照する。

```js title=".vscode/settings.json"
{
    "clangd.arguments": [
        "--compile-commands-dir=${workspaceFolder}/builddir"
    ],
}
```

:::

:::note
ビルドディレクトリが cmake と被らないように builddir を使う。

https://marketplace.visualstudio.com/items?itemName=mesonbuild.mesonbuild

がデフォルトで builddir を使う。
task も builddir にしておいた。
:::

## project init

空プロジェクトの生成

https://mesonbuild.com/Project-templates.html


