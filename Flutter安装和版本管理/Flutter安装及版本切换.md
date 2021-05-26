[TOC]

## flutter安装

有两种安装方式：git clone 和 macOS brew install。

### 推荐安装方式（克隆仓库）

将 flutter github 仓库克隆到本地目录，例如 macOS 下的 `~/Library/Developer/`：

```
$ git clone https://github.com/flutter/flutter.git ~/Library/Developer/
```

然后需要配置环境变量，macOS 下在 `~/.zshrc` 中添加以下三行：

```
export PATH=$HOME/Library/Developer/flutter:$PATH
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
```

> 当前终端shell窗口需要执行 `. ~/.zshrc` 或 `source ~/.zshrc` 才能生效，或者新建shell窗口立即生效。

### macOS下通过 brew 安装

macOS 下还可通过 homebrew 包管理器工具来安装，具体来说执行 `brew install --cask flutter` 命令即可。

由于brew安装flutter后，会将 flutter 和 dart 二进制可执行文件软链一份到 */usr/local/bin* 下，而该目录默认已在 PATH 中，因此无需额外配置 `PATH` 环境变量。
但是仍需配置 `PUB_HOSTED_URL` 和 `FLUTTER_STORAGE_BASE_URL` 这两个环境变量。

---

brew 安装 flutter 后，执行 `which flutter` 查看其 binutils 位置：

```
$ which flutter
/usr/local/bin/flutter
```

执行 ls -F 可知其为软链（symbol link）

```
$ ls -F `which flutter`
/usr/local/bin/flutter@
```

进一步执行 readlink 可读取其原身位置，此即 brew 安装位置：

```
$ readlink `which flutter`
/usr/local/Caskroom/flutter/1.22.6/flutter/bin/flutter
```

cd 到安装目录，其实也是 flutter github 仓库：

```
$ cd /usr/local/Caskroom/flutter/1.22.6/flutter/
$ git remtoe -v
origin	https://github.com/flutter/flutter.git (fetch)
origin	https://github.com/flutter/flutter.git (push)
```

### 官方release包

除此之外，可以到 flutter 官方网站 [Install](https://flutter.dev/docs/get-started/install) 下载安装包，在 [Flutter SDK releases](https://flutter.dev/docs/development/tools/sdk/releases) 中查找下载指定历史版本。

如果官方被墙或网速很慢，上 flutter.cn 下载更快：https://flutter.cn/docs/development/tools/sdk/releases?tab=macos, 例如 https://storage.flutter-io.cn/flutter_infra/releases/stable/macos/flutter_macos_1.22.6-stable.zip 。

## flutter通道及发行版本

### flutter channel

执行 `flutter channel` 查看当前通道：

```
$ flutter channel
Flutter channels:
  master
  dev
  beta
* stable
```

> 下面通过 git checkout 或者 git reset 强切版本，flutter git 仓库将处于 'detached HEAD' 状态（例如 HEAD detached at 1.22.6），将没有星号标记当前 channel。

### git tag

flutter release 发版一般都会有对应的tag，cd进入本地 flutter git 仓库，然后执行 `git tag` 可查看发行版本。

```
$ cd ~/Library/Developer/flutter # cd /usr/local/Caskroom/flutter/1.22.6/flutter/
$ git tag | sort | grep '^1\.*\|^2\.*'
# 此处省略部分低版本输出
1.22.1
1.22.2
1.22.3
1.22.4
1.22.5
1.22.6
1.23.0-13.0.pre
1.23.0-18.0.pre
1.23.0-18.1.pre
1.23.0-3.0.pre
1.23.0-4.0.pre
1.23.0-7.0.pre
1.24.0-1.0.pre
1.24.0-10.1.pre
1.24.0-10.2.pre
1.24.0-3.0.pre
1.24.0-6.0.pre
1.24.0-7.0.pre
1.25.0-4.0.pre
1.25.0-8.0.pre
1.25.0-8.1.pre
1.25.0-8.2.pre
1.25.0-8.3.pre
1.26.0-1.0.pre
1.26.0-12.0.pre
1.26.0-17.0.pre
1.26.0-17.1.pre
1.26.0-17.2.pre
1.26.0-17.3.pre
1.26.0-17.4.pre
1.26.0-17.5.pre
1.26.0-17.6.pre
1.26.0-17.7.pre
1.26.0-17.8.pre
1.26.0-8.0.pre
1.27.0-1.0.pre
1.27.0-4.0.pre
1.27.0-8.0.pre
2.0.0
2.0.1
2.0.2
2.0.3
2.0.4
2.0.5
2.1.0-10.0.pre
2.1.0-12.0.pre
2.1.0-12.1.pre
2.1.0-12.2.pre
2.2.0-10.0.pre
2.2.0-10.1.pre
```

## flutter切换版本

强烈建议采用 `FVM`，参考 [Flutter版本管理器-FVM](url)。

### downgrade

目前通过 git clone 或 brew install flutter，其 stable 版本已经到了 2.0.5-2.12.3。

```
$ flutter --version
Flutter 2.0.5 • channel stable • https://github.com/flutter/flutter.git
Framework • revision adc687823a (7 hours ago) • 2021-04-16 09:40:20 -0700
Engine • revision b09f014e96
Tools • Dart 2.12.3
```

由于我们的 flutter_module 工程还没有完全适配 flutter 2.0，需要将其回退到 last stable 版本 1.22.6。

一般通过如下三（四）步即可实现版本降级：

```
# 1. 切换到 stable 频道
flutter channel stable
# 2. 降级到 1.22.6
flutter downgrade v1.22.6 # git checkout 1.22.6 or git reset --hard 1.22.6
# 3. 删除工具链缓存（可选）
rm -rf bin/cache
# 4. 重新拉取工具链
flutter precache # or flutter doctor?
```

正常执行 `flutter downgrade` 的版本提示如下：

```
$ flutter downgrade v1.22.6
Downgrade flutter to version 1.22.6
? [y|n]: y
```

输入 `y` 确认即可成功降级。

如果执行 `flutter downgrade` 的输出报错或确认版本非预期：

```
$ flutter downgrade v1.22.6
Downgrade flutter to version 1.22.4
? [y|n]: n
```

则可cd到flutter本地git仓库目录，然后通过 `git checkout` 或 `git reset` 来切换版本到指定tag：

```
$ cd ~/Library/Developer/flutter # cd /usr/local/Caskroom/flutter/1.22.6/flutter/
$ git checkout 1.22.6
Updating files: 100% (3185/3185), done.
Note: switching to '1.22.6'.
```

以上通过 flutter downgrade 命令或 git checkout 命令切换 flutter 版本后，一般需执行 `flutter precache` 重新拉取工具链：

```
$ flutter precache
Downloading Dart SDK from Flutter engine 2f0af3715217a0c2ada72c717d4ed9178d68f6ed...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  172M  100  172M    0     0  5454k      0  0:00:32  0:00:32 --:--:-- 5855k
Building flutter tool...
Failed to download the Android dependencies
Downloading Android Maven dependencies...                           1.0s
Flutter assets will be downloaded from https://storage.flutter-io.cn. Make sure you trust this source!
Downloading android-arm-profile/darwin-x64 tools...                 1.0s
Downloading android-arm-release/darwin-x64 tools...                 1.0s
Downloading android-arm64-profile/darwin-x64 tools...               0.8s
Downloading android-arm64-release/darwin-x64 tools...               0.9s
Downloading android-x64-profile/darwin-x64 tools...                 0.9s
Downloading android-x64-release/darwin-x64 tools...                 0.9s
Downloading android-x86 tools...                                    5.0s
Downloading android-x64 tools...                                    4.9s
Downloading android-arm tools...                                    2.7s
Downloading android-arm-profile tools...                            1.2s
Downloading android-arm-release tools...                            1.0s
Downloading android-arm64 tools...                                  2.1s
Downloading android-arm64-profile tools...                          1.5s
Downloading android-arm64-release tools...                          1.3s
Downloading android-x64-profile tools...                            1.4s
Downloading android-x64-release tools...                            1.1s
Downloading android-x86-jit-release tools...                        1.9s
Downloading ios tools...                                            8.7s
Downloading ios-profile tools...                                    5.9s
Downloading ios-release tools...                                   37.1s
Downloading package sky_engine...                                   0.3s
Downloading flutter_patched_sdk tools...                            3.3s
Downloading flutter_patched_sdk_product tools...                    3.1s
Downloading darwin-x64 tools...                                     7.8s
Downloading darwin-x64/font-subset tools...                         0.7s
```

> 可执行 `flutter doctor -v` 检查还有哪些工具链或配置缺失，按照提示指引进行补齐。

正常情况下，执行 `flutter --version` 将显示已经降级到 1.22.6-2.10.5：

```
$ flutter --version
Flutter 1.22.6 • channel unknown • unknown source
Framework • revision 9b2d32b605 (3 months ago) • 2021-01-22 14:36:39 -0800
Engine • revision 2f0af37152
Tools • Dart 2.10.5
```

### upgrade

首先，切换channel为stable，然后执行 `flutter upgrade` 即可升级到当前stable channel的最新版本。

```
$ flutter channel stable
$ flutter upgrade
```

upgrade 貌似不能像 downgrade 命令那样指定版本？

如果要升级到中间某个版本，还是可以采用 `git checkout` 或 `git reset` 方式，切换后执行 `flutter precache` 重新拉取工具链。

### clean

切换flutter版本后，需进入 flutter_module 执行一次 `flutter clean`，然后再执行 `flutter pub get` 重新拉取依赖 packages。

> 有时遇到某些缓存问题，也可手工清理 `flutter/.pub-cache` 目录，重新 flutter pub get 全量更新。

## Q&A

### pod install 报错找不到  Flutter.framework

iOS 壳工程执行 pod install 报错找不到  `Flutter.framework`。

> 出现工具链或引擎缺失问题，请先检查工具链 `flutter/bin/cache/` 下的 `artifacts/engine` 等是否完整。

一般可通过移除工具链缓存 `flutter/bin/cache/`，执行 `flutter precache` 重拉工具链解决。

### pod install 报错找不到 Flutter.podspec

参考 [No podspec found](https://www.jianshu.com/p/b981889122aa)，执行 pod install 报错找不到 `flutter_module/.ios/Flutter/engine/Flutter.podspec`：

```
    Fetching external sources
    -> Fetching podspec for `Flutter` from `../flutter_module/.ios/Flutter/engine`
    [!] No podspec found for `Flutter` in `../flutter_module/.ios/Flutter/engine`
```

此时，可移除 flutter_module 下的 `.ios` 目录，然后重新执行 `flutter pub get`；再回到 iOS 壳工程目录重新执行 `pod install`。

### 运行报错 Dart Error: Can't load Kernel binary

upgrade到2.0.4，再downgrade回1.22.6，编译没问题，但是启动运行一直报错：

![DartError#80%](/download/attachments/673332863/image-1622069314592.png?version=1&modificationDate=1622069314672&api=v2)

参考 [Flutter iOS 混合开发](https://www.jianshu.com/p/93fa2c90bed3)：

- *问题*: Dart Error: Can't load Kernel binary: Invalid kernel binary format version。
- *原因*: flutter 和 engine 版本不一致，需要更新到一致状态。

**可尝试以下方式解决**：

1. 依次尝试重启终端、vscode、IDE（Xcode/AndroidStudio）、电脑；  
2. 尝试清除HOME目录下的所有 `.flutter`、`.dart`、`.pub-cache`，然后重新 flutter clean、flutter pub get；  
3. 尝试卸载flutter（删除git仓库或 `brew uninstall flutter`），重新安装后再downgrade切换。

## refs

[How to downgrade Flutter SDK (Dart 1.x)](https://stackoverflow.com/questions/49468321/how-to-downgrade-flutter-sdk-dart-1-x)  
[Flutter cannot downgrade SDK version from 1.22.x to 1.20.0 #65175](https://github.com/flutter/flutter/issues/65175)  
[Flutter-常见问题](https://blog.csdn.net/zhanshenzhi2008/article/details/109487912)  