
从 pub.dev 插件主页上提供的 Repository (GitHub) 下载源码到本地，cd 进入 example 目录执行 `flutter run` 即可运行插件 demo（lib/main.dart）。

## flutter-run

**flutter run** 背后会自动执行 `flutter pub get` 安装依赖，如果检测到联机设备是iOS，则接下来依次执行以下步骤：

1. certificate choice：检测本地安装的开发者证书，如果有多个需交互选择；  
2. signing app：使用developer identity配置APP的签名；  
3. pod install：安装Podfile中描述的原生依赖；  
4. xcodebuild：执行Xcode编译。  

> **说明**：Android 稍有不同，请自行对照参考。

### 分解步骤

也可不执行一步到位的 `flutter run`，自行执行安装 flutter 和原生平台依赖（cocoapods、gradle）、配置签名（iOS）、打开IDE编译等步骤。

以 [fluttertoast](https://pub.dev/packages/fluttertoast) 插件为例，首先需要打开 pubspec.yaml，检查 *environment* 中的 flutter sdk 版本。
如果当前安装的 flutter SDK 版本过低，考虑使用 [FVM](https://blog.csdn.net/phunxm/article/details/117317291) 切换到插件要求的最低支持版本。

```
# Step1: 直接在主目录或cd进入example目录，执行 pub get 安装flutter package依赖：
cd example && flutter pub get
# Step2: cd进入ios目录，执行 pod install 安装 iOS 原生依赖：
cd ios && pod install
# Step3: 在Xcode IDE中打开工程
open Runner.xcworkspace
# Step4: 在Xcode IDE中配置 Signing
# 按需修改 Display Name、Display Identifier，然后执行编译
```

> [toasty](https://pub.dev/packages/toasty)、[fluttertoast](https://pub.dev/packages/fluttertoast)、[fluttertoastalert](https://pub.dev/packages/fluttertoastalert)、[flutter_flexible_toast](https://pub.dev/packages/flutter_flexible_toast) 等依赖 methodChannel 原生实现的插件，一般要执行 pod install。例如 fluttertoast pod 依赖了 `SCLAlertView`。对于纯 Flutter 工程，则无需执行 `pod install`。

但是，有些插件的demo工程可能基于较旧的flutter SDK创建，导致无法编译运行，下文为pub.dev中的部分插件编译遇到的问题及解决方案。

## flutter_smart_dialog

[flutter_smart_dialog](https://pub.dev/packages/flutter_smart_dialog) 源码样例，在 example 下执行 `flutter pub get` 将会报错：

```
Running "flutter pub get" in example...
Because example depends on flutter_smart_dialog from path which doesn't exist (could not find package flutter_smart_dialog at "..\..\flutter_smart_dialog"), version solving failed.
```

这是因为文件路径分隔符（python中的os.sep）的识别问题：在Windows平台上是 `\`，在Linux/macOS平台上则是 `/`，macOS平台下无法识别Windows平台的路径写法。

将 `example/pubspec.yaml` 如下：

```
dependencies:

  flutter_smart_dialog:
    path: ..\..\flutter_smart_dialog

```

macOS 下可修改为如下：

```
dependencies:

  flutter_smart_dialog:
    path: ../

  # 或者直接使用版本
  # flutter_smart_dialog: ^2.0.5

```

重新执行 `flutter pub get`、`flutter run` 即可运行起demo。

## ftoast

ftoast 为 [Fliggy-Mobile](https://github.com/Fliggy-Mobile) 系列组件之一。

从 [ftoast](https://pub.dev/packages/ftoast) 插件主页上提供的 [Repository (GitHub)](https://github.com/Fliggy-Mobile/ftoast) 链接下载源码到本地，exampe 下只有一个 lib 目录，没有 ios/android 目录。

根据pub.dev主页最后一段的说明，需要进入 example 目录执行 `flutter create .`，然后再执行 `flutter run` 即可运行起demo。

---

如果公司内不支持 SSH，则需要将 example/pubspec.yaml 中 fcommon.git 地址中的 SSH url 修改为 https url。

将

```
      url: 'git@github.com:Fliggy-Mobile/fcommon.git'
```

替换为：

```
      url: 'https://github.com/Fliggy-Mobile/fcommon.git'
```

## toast

从 [toast](https://pub.dev/packages/toast) 插件主页上提供的 [Repository (GitHub)](https://github.com/appdev/FlutterToast) 链接下载源码到本地，exampe 下只有一个 lib 目录，没有 ios/android目录。

参考 [ftoast](https://pub.dev/packages/ftoast)，进入 example 目录下执行 `flutter create .`

执行 `open ios/Runner.xcworkspace` 打开 Xcode 工程，配置好证书后编译（或  `flutter run` ）报错：

![toast-Not-Found](https://img-blog.csdnimg.cn/2021053021585967.png)

**解决方案**：修改 `example/pubspec.yaml`，在 dependencies 或 dev_dependencies 分组中添加 toast 插件本地相对路径：

```yaml
dev_dependencies:
  flutter_test:
    sdk: flutter
  toast:
    path: ../
```

然后再执行 `flutter run` 即可运行起demo。

## oktoast

从 [oktoast](https://pub.dev/packages/oktoast) 插件主页上提供的 [Repository (GitHub)](https://github.com/OpenFlutter/flutter_oktoast) 链接下载源码到本地，example 编译报错：

![oktoast-No-Such-File](https://img-blog.csdnimg.cn/20210530220021283.png)

可在本地重新创建 example 解决。

1. 备份原有 example：`mv example example0`；  
2. 重新创建 example：`flutter create example`；  
3. 复制原有 example 下的依赖配置和测试代码：

    - `cp example0/pubspec.yaml example/pubspec.yaml`  
    - `cp -r example0/lib/* example/lib/`  
    - cp 其他资源文件...  

4. 检查 `example/pubspec.yaml`，在 dependencies 或 dev_dependencies 分组中按需添加 oktoast 插件的本地相对路径。
5. 由于修改了 `pubspec.yaml` 文件，需重新执行 `flutter pub get` 更新依赖包。  

6. 打开Xcode工程，配置好签名（Signing），即可编译运行。  

> 完整测试用例参考 [oktoast](https://www.kikt.top/posts/flutter/toast/oktoast/)，部分代码略加修改支持 Null safety 即可运行。

**说明**：

1. 如果用 FVM 配置了 per-project 的 flutter SDK，则执行相关 flutter 命令需加上 fvm 代理前缀。例如用 `fvm flutter create example` 代替 `flutter create example`。  
2. 如果 xcodebuild 遇到诸如 `Invalid depfile: ~/oktoast/example/.dart_tool/flutter_build/6f462094efa2e3a279f832b5facc24d0/kernel_snapshot.d` 此类错误，则考虑执行 `flutter clean` 后重新 `flutter pub get`。  

---

[flutter_styled_toast](https://pub.dev/packages/flutter_styled_toast)、[bot_toast](https://pub.dev/packages/bot_toast)、[flutter_easyloading](https://pub.dev/packages/flutter_easyloading)、[easy_alert](https://pub.dev/packages/easy_alert)、[textfield_tags](https://pub.dev/packages/textfield_tags) 等插件 example 无法运行问题均可参考以上处理。

## 参考

[Flutter Plugin开发流程](https://www.jianshu.com/p/4bf045fd21f5)  
[Flutter插件iOS端开发教程](https://juejin.cn/post/6844904072969994248)  
[flutter-发布一个package到pub.dev 超详细教程](https://blog.csdn.net/ai_pple/article/details/108535418)  
[How can I run an example from a Flutter package/library?](https://stackoverflow.com/questions/53785710/how-can-i-run-an-example-from-a-flutter-package-library)  
