
在上一篇 [Flutter安装及版本切换](https://blog.csdn.net/phunxm/article/details/117316761) 中，主要介绍了 Flutter SDK 的安装以及通过 git 命令行切换 Flutter SDK 版本。命令行切换成本较高，且经常会遇到各种工具链问题。

类似 [pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv)、[RVM](http://rvm.io/)、[NVM](https://github.com/nvm-sh/nvm) 等各种 VM（Version Manager），Flutter 领域也有 `FVM`，提供了便利的 per-project 配置和 global 版本管理功能。

## fvm

[fvm](https://fvm.app/) @[github](https://github.com/leoafarias/fvm)

`FVM` helps with the need for consistent app builds by referencing the Flutter SDK version used on a per-project basis. It also allows you to have multiple Flutter versions installed to quickly validate and test upcoming Flutter releases with your apps without waiting for Flutter installation every time.

- [Flutter 多个版本切换控制](https://mp.weixin.qq.com/s/fxEgIw521W707yDu2VJBTw)  
- [用fvm管理多个Flutter SDK](https://mp.weixin.qq.com/s/NG5llgedIjP316uZwa-QGw)  
- [win10使用fvm切换Flutter版本](https://blog.csdn.net/PyMuma/article/details/115298645)  

下文主要串讲通过 fvm 命令下载和切换 Flutter SDK 的操作流程。
如嫌命令行麻烦，也可改用 GUI 版的管理工具 [Sidekick](https://github.com/leoafarias/sidekick)。

### 安装 fvm

参考 [installation](https://fvm.app/docs/getting_started/installation)。

macOS 下建议使用 brew 安装：

```
brew tap leoafarias/fvm
brew install fvm
```

brew 卸载命令：

```
brew uninstall fvm
brew untap leoafarias/fvm
```

### 配置 FVM_HOME

参考 [configuration](https://fvm.app/docs/getting_started/configuration)。

安装fvm后，考虑设置环境变量 `FVM_HOME` 或 `FVM_GIT_CACHE`，否则默认安装 flutter SDK 缓存路径为 `~/fvm/versions`；  

## fvm releases

`fvm releases`: View all Flutter SDK releases available for install.

列举远程可用的 flutter SDK 发行版。

```
$ fvm releases

...
Jan 25 21  │ 1.22.6
Jan 29 21  │ 1.26.0-17.1.pre
Feb 4 21   │ 1.26.0-17.2.pre
Feb 4 21   │ 1.26.0-17.2.pre
Feb 5 21   │ 1.26.0-17.3.pre
Feb 10 21  │ 1.26.0-17.4.pre
Feb 10 21  │ 1.27.0-1.0.pre
Feb 11 21  │ 1.26.0-17.5.pre
Feb 16 21  │ 1.26.0-17.6.pre
Feb 17 21  │ 1.27.0-4.0.pre
Feb 24 21  │ 1.26.0-17.7.pre
Feb 24 21  │ 1.27.0-8.0.pre
Feb 25 21  │ 1.26.0-17.8.pre
Mar 3 21   │ 2.0.0
Mar 3 21   │ 2.1.0-10.0.pre
Mar 3 21   │ 2.0.0
Mar 4 21   │ 2.0.1
Mar 4 21   │ 2.0.1
Mar 12 21  │ 2.0.2
Mar 13 21  │ 2.1.0-12.1.pre
Mar 15 21  │ 2.0.2
Mar 18 21  │ 2.1.0-12.2.pre
Mar 19 21  │ 2.0.3
Apr 2 21   │ 2.0.4
Apr 15 21  │ 2.2.0-10.1.pre
Apr 15 21  │ 2.2.0-10.1.pre
Apr 16 21  │ 2.0.5
Apr 27 21  │ 2.3.0-0.1.pre
Apr 29 21  │ 2.2.0-10.2.pre
Apr 30 21  │ 2.0.6
May 10 21  │ 2.2.0-10.3.pre
May 10 21  │ 2.3.0-1.0.pre
--------------------------------------
May 18 21  │ 2.2.0             stable
--------------------------------------
--------------------------------------
May 18 21  │ 2.3.0-12.1.pre    dev
--------------------------------------
--------------------------------------
May 19 21  │ 2.2.0             beta
--------------------------------------
```

## fvm install

`fvm install`: Installs Flutter SDK Version. Gives you the ability to install Flutter `releases` or `channels`.

- `fvm install dev/beta/stable/...`: 安装指定channel的当前版本 flutter SDK；  
- `fvm install 2.2.0`: 安装指定版本（2.2.0）的 flutter SDK；  

> 如果没有配置 `FVM_HOME` 或 `FVM_GIT_CACHE` 环境变量，则默认安装到 `~/fvm/versions/` 目录下。

以下安装指定版本和channel：

- `fvm install 1.22.6`：安装 flutter 2.0 之前最后一个 stable 版本 1.22.6；  
- `fvm install stable`：由于当前的 stable 为 2.2.0，安装的 stable 等效于 `fvm install 2.2.0`；  
- `fvm install dev`：安装最新的 dev 2.3.0-12.1.pre 预览版本，以便完成一些实验性功能开发和验证；  

执行了 `fvm install 2.2.0` 后可执行 `fvm remove 2.2.0` 卸载上安装的 2.2.0 版本。

```
fantasy@MBP ~ $ tree -L 1 ~/fvm/versions
/Users/fantasy/fvm/versions
├── 1.22.6
├── dev
└── stable

3 directories, 0 files
```

## fvm list

列举查看本地已安装的版本。

```
fantasy@MBP ~ $ fvm list
Cache Directory:  /Users/fantasy/fvm/versions

stable
dev
1.22.6
```

## fvm use

在当前 flutter_project 目录执行 `fvm use version` 即可切换flutter SDK版本。

FVM will create a relative *symlink* in your project from `.fvm/flutter_sdk` to the cache of the selected version. Add it to your `.gitignore`.

> 当前目录下会生成一个 `.fvm` 文件夹，考虑将 `.fvm/*` 添加到 .gitignore 文件，避免误传上库。

在flutter项目 my_flutter_project 下执行 `fvm use stable` 切换 flutter SDK 为最新 stable 版本：

```
fantasy@MBP ~/Projects//my_flutter_project $ fvm use stable
Project now uses Flutter [stable]
```

> 其中 `.fvm/flutter_sdk` 将软链到 `~/fvm/versions/stable`；配置文件 `.fvm/fvm_config.json` 中同步记录了  `"flutterSdkVersion": "stable"`。

接下来，可用 `fvm flutter` 代替 `flutter` 命令，以便自动pick当前repo所用的SDK。

---

在当前项目目录下执行 `fvm list`，当前正在使用的 flutter SDK 版本将会标识为 *active*：

```
fantasy@MBP ~/Projects//my_flutter_project $ fvm list
Cache Directory:  /Users/fantasy/fvm/versions

stable (active)
dev
1.22.6
```

在当前项目目录下执行 `fvm flutter --version`，可查看使用的 flutter SDK 版本信息：

```
fantasy@MBP ~/Projects//my_flutter_project $ fvm flutter --version
Flutter 2.2.0 • channel stable • https://github.com/flutter/flutter.git
Framework • revision b22742018b (11 days ago) • 2021-05-14 19:12:57 -0700
Engine • revision a9d88a4d18
Tools • Dart 2.13.0
```

执行其他代理命令安装依赖包更新、运行：

```
fvm flutter clean
fvm flutter pub get
fvm flutter run
```

## fvm flavor

`project flavors`：某个项目外发版本还是用稳定的 1.22.6，但是 flutter 2 的适配工作同步进行中，则可为该项目创建多个 flavor，方便主干和适配分支切换 flavors 中预配的 flutter SDK 版本。

### Pin flavor version

指定某个 flutter SDK 版本为 flavor 别名。

To choose a Flutter SDK version for a specific flavor you just use the `use` command.

```
fvm use {version} --flavor {flavor_name}
```

This will pin `version` to `flavor_name`，flavors 映射（flavor_name -> version）将被记录到配置文件 `.fvm/fvm_config.json` 中。

```
fantasy@MBP ~/Projects//my_flutter_project $ fvm use 1.22.6 --flavor production
Project now uses Flutter [1.22.6] on [production] flavor.

fantasy@MBP ~/Projects//my_flutter_project $ fvm use stable --flavor stable
Project now uses Flutter [stable] on [stable] flavor.

fantasy@MBP ~/Projects//my_flutter_project $ fvm use dev --flavor dev
Project now uses Flutter [dev] on [dev] flavor.

fantasy@MBP ~/Projects//my_flutter_project $ cat .fvm/fvm_config.json
{
  "flutterSdkVersion": "stable",
  "flavors": {
    "production": "1.22.6",
    "stable": "stable",
    "dev": "dev"
  }
}
```

### View flavors

列举当前项目配置的 flavors（To list all configured flavors）。

```
fantasy@MBP ~/Projects//my_flutter_project $ fvm flavor
Project flavors configured for "my_flutter_project":

[1] production
[2] dev
[3] stable

Select an environment: ^C
```

按照提示，可在 Select an environment 后面输入 `2` 或 `dev` 切换到 dev 开发环境。如果仅为查看，可按下 Ctrl+C 退出。

### Switch flavors

Will get the version configured for the flavor and set as the project version.

```
fvm flavor {flavor_name}
```

为当前项目切换到 *flavor_name* 对应的版本。

例如 `fvm flavor production` 将切换到 1.22.6 版本，等效于 `fvm use 1.22.6`。 

> 切换后，`.fvm/fvm_config.json` 中的 flutterSdkVersion 将同步更新为 `1.22.6`。

## IDE配置

### vscode

可全局配置 vscode（`～/Library/Application\ Support/Code\ -\ Insiders/User/settings.json`）：

```
{
  "dart.flutterSdkPaths": ["/Users/usr/fvm/versions"]
}
```

或

```
{
  "dart.flutterSdkPaths": [
    "/Users/usr/fvm/versions/stable",
    "/Users/usr/fvm/versions/dev"
  ]
}
```

则可在 vscode 控制面板中执行 `Flutter: Change SDK` 切换 Flutter SDK 版本，将同步记录到当前项目的 vscode 配置文件 `.vscode/settings.json` 中（dart.flutterSdkPath）。

---

也可直接编辑当前项目的 vscode 配置文件 `.vscode/settings.json`：

```json
{
  "dart.flutterSdkPath": ".fvm/flutter_sdk",
  // or "dart.flutterSdkPaths": [".fvm/flutter_sdk"]
  
  // Remove .fvm files from search
  "search.exclude": {
    "**/.fvm": true
  },
  // Remove from file watching
  "files.watcherExclude": {
    "**/.fvm": true
  }
}
```

### AndroidStudio

参考 [configuration](https://fvm.app/docs/getting_started/configuration)。

## fvm global

如果APP目前还未完成适配flutter 2.0，暂时可将 1.22.6 设置为全局主力版本。

执行 `fvm global 1.22.6`，提示需要将相关路径添加到 PATH：

```
fantasy@MBP ~ $ fvm global 1.22.6
Flutter "1.22.6" has been set as global
However your "flutter" path current points to:

.
to use global Flutter SDK through FVM you should change it to:

/Users/fantasy/fvm/default/bin
```

`vim ~/.zshrc` 打开编辑 zsh 配置，将 default Flutter SDK 可执行文件所在路径添加到环境变量 PATH：

```
export PATH=$HOME/fvm/default/bin:$PATH
```

> 进入 `～/fvm` 目录可以看到，default 实际上是 `versions/1.22.6` 的替身软链。

如果iOS工程报以下错误，考虑执行 `flutter precache` 重拉工具链解决。

```
[!] Invalid `Podfile` file: No such file or directory @ rb_file_s_stat - /Users/fantasy/fvm/versions/1.22.6/bin/cache/artifacts/engine/ios/Flutter.framework.
```

重新执行 `fvm list`，可以看到 1.22.6 已经被标识为 *global*：

```
fantasy@MBP ~ $ fvm list
Cache Directory:  /Users/fantasy/fvm/versions

stable
beta
1.22.6 (global)
```

此时，执行 `flutter --version`，将显示全局版本为 `Flutter 1.22.6, Dart 2.10.5`。

## fvm taps

以下梳理网上比较常见的两个民间 fvm，可选使用。

### dashixiong91

[基于 Flutter 的⼩程序框架实践](https://techsummit.ctrip.com/pdf/renyujie.pdf) 文末介绍 <s>xinfeng-tech/fvm</s>

[dashixiong91 / fvm](https://github.com/dashixiong91/fvm) - [dashixiong91 / homebrew-fvm](https://github.com/dashixiong91/homebrew-fvm)

```
brew tap dashixiong91/fvm
brew install fvm
```

- [Flutter Versions Manager](https://www.xin1t.com/blog/post/flutter-versions-manager/)  
- [Flutter SDK 多版本管理工具](https://zhuanlan.zhihu.com/p/89469225)  

以下基于旧的 <s>xinfeng-tech/fvm</s>：

- [使用fvm助力你吃上Flutter2](https://juejin.cn/post/6948266183397212174) - vscode 配置  
- [FVM - Mac上管理 Flutter 多版本的神器](https://juejin.cn/post/6939712499465846798) - Android Studio 配置  

### befovy

[go-fvm：flutter 版本切换助手](https://www.lmonkey.com/t/oZBdajkEp)：[befovy / fvm](https://github.com/befovy/fvm)

其核心逻辑就是在本地文件夹中缓存多个 Flutter 版本，并为项目创建指定 Flutter 版本的软链接。或者在全局环境创建指定版本的 Flutter 软链接。

```
brew tap befovy/taps
brew install fvm
```

[使用fvm 管理多版本flutter](https://www.jianshu.com/p/3126b145f93e)  
[使用Flutter包管理工具fvm过程中遇到的问题](https://www.jianshu.com/p/8f792a4755bf)  
