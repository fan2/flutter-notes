
正常情况，从 pub.dev 插件 [toast](https://pub.dev/packages/toast) 主页上的 Repository (GitHub) 下载源码到本地，cd 进入 example 目录执行 `flutter pub get` 安装依赖，然后再执行 `flutter run` 即可运行 demo。

但是，有些工程创建demo时，可能flutter SDK版本较旧，导致各种编译问题。

## toast 插件 example 无法运行

从 github 下载 [toast](https://pub.dev/packages/toast) 源码到本地，cd进入example目录，执行 `flutter pub get` 成功。
执行 `open ios/Runner.xcworkspace` 打开 Xcode 工程，配置好证书，编译报错：

![toast-Not-Found](https://img-blog.csdnimg.cn/2021053021585967.png)

**解决方案**：修改 `example/pubspec.yaml`，在 dependencies 或 dev_dependencies 分组中添加 toast 插件本地相对路径：

```yaml
dev_dependencies:
  flutter_test:
    sdk: flutter
  toast:
    path: ../
```

---

[flutter_smart_dialog](https://pub.dev/packages/flutter_smart_dialog) 样例  `example/pubspec.yaml` 如下：

```
dependencies:

  flutter_smart_dialog:
    path: ..\..\flutter_smart_dialog

```

macOS 下可修改为：

```
dependencies:

  flutter_smart_dialog:
    path: ../

  # 或者
  # flutter_smart_dialog: ^2.0.5

```

## oktoast 插件 example 无法运行

从 github 下载 [oktoast](https://pub.dev/packages/oktoast) 源码到本地，example 编译报错：
![oktoast-No-Such-File](https://img-blog.csdnimg.cn/20210530220021283.png)
可在本地重新创建 example，修改 `example/pubspec.yaml` 解决。

1. 备份原有 example：`mv example example0`；  
2. 重新创建 example：`flutter create example`；  
3. 复制原有 example/lib/ 下的测试代码：`cp -r example0/lib/* example/lib/`；  
4. 修改 `example/pubspec.yaml`，在 dependencies 或 dev_dependencies 分组中添加 oktoast 插件本地相对路径，或直接 dependencies 下指定 `oktoast: ^3.0.0`；  
5. 打开Xcode工程，配置好签名（Signing），即可编译运行。  

> [flutter_easyloading](https://pub.dev/packages/flutter_easyloading) 插件 example 的运行可参考以上处理。

## 参考

[How can I run an example from a Flutter package/library?](https://stackoverflow.com/questions/53785710/how-can-i-run-an-example-from-a-flutter-package-library)  
[flutter-发布一个package到pub.dev 超详细教程](https://blog.csdn.net/ai_pple/article/details/108535418)  
