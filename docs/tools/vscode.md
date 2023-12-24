# vscode

https://marketplace.visualstudio.com/items?itemName=mesonbuild.mesonbuild

この拡張でシンタックスハイライトなどが有効になる。
formatter を有効にするには、

https://muon.build/

をビルドして関連設定をする。
Windows でも `msys` ならビルドできた。
`msys` の `/usr/bin` に `muon.exe` を copy するとよさそう。

```settings.json
{
    "mesonbuild.muonPath": "C:/msys64/usr/bin/muon.exe",
    "mesonbuild.formatting.enabled": true,
    "mesonbuild.linter.muon.enabled": true,
    "mesonbuild.linting.enabled": true,
}
```


