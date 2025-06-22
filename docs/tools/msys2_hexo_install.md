## 1. 更新 pacman 包列表

```bash
pacman -Syu
```

## 2. 安装Node.js

通过如下命令查看pacmac包中包含的nodejs的安装包

```
pacman -Sl | grep nodejs
```

输出如下

```text
$ pacman -Sl | grep nodejs
clangarm64 mingw-w64-clang-aarch64-nodejs 23.5.0-3
clangarm64 mingw-w64-clang-aarch64-nodejs-neovim 5.3.0-1
clangarm64 mingw-w64-clang-aarch64-nodejs-webpack-cli 6.0.1-1
clangarm64 mingw-w64-clang-aarch64-python-hatch-nodejs-version 0.3.2-2
mingw32 mingw-w64-i686-python-hatch-nodejs-version 0.3.2-2
mingw64 mingw-w64-x86_64-nodejs 23.5.0-3 [installed]
mingw64 mingw-w64-x86_64-nodejs-webpack-cli 6.0.1-1
mingw64 mingw-w64-x86_64-python-hatch-nodejs-version 0.3.2-2
ucrt64 mingw-w64-ucrt-x86_64-nodejs 23.5.0-3
ucrt64 mingw-w64-ucrt-x86_64-nodejs-neovim 5.3.0-1
ucrt64 mingw-w64-ucrt-x86_64-nodejs-webpack-cli 6.0.1-1
ucrt64 mingw-w64-ucrt-x86_64-python-hatch-nodejs-version 0.3.2-2
clang64 mingw-w64-clang-x86_64-nodejs 23.5.0-3
clang64 mingw-w64-clang-x86_64-nodejs-neovim 5.3.0-1
clang64 mingw-w64-clang-x86_64-nodejs-webpack-cli 6.0.1-1
clang64 mingw-w64-clang-x86_64-python-hatch-nodejs-version 0.3.2-2
```

通过如下命令安装 `nodejs`

```bash
pacman -S mingw-w64-x86_64-nodejs
```

## 3. 验证node和npm安装

```bash
$ node -v
v23.5.0

$ npm -v
10.9.2

```

## 4. 安装hexo

```bash
npm install -g hexo-cli
```