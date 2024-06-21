---
title:       "Flutter桌面应用开发：创建并运行桌面应用"
subtitle:    ""
description: ""
date:        2019-12-01
author:      "xtcel"
image:       ""
tags:        ["Flutter", "Flutter桌面"]
categories:  ["Tech" ]

---

在Google I/O 2019上Flutter 团队宣布推出最新稳定版: Flutter 1.9。这是 Flutter 迄今为止最大的一次版本更新，Flutter 1.9 引入的新特性与更新涵盖范围广泛，包括 macOS Catalina 和 iOS 13 支持、工具支持优化、多项 Dart 语言新特性以及全新的 Material widget。在本文中，我们将详细介绍在桌面环境中运行一个新的或现有的Flutter应用程序的过程。

让我们开始吧!
在我的尝试中，我发现有多种方法可以做到这一点，所以为了简单起见，我们选择最简单的一种。

### 搭建环境

先安装Flutter SDK
[https://flutter.dev/docs/get-started/install](https://flutter.dev/docs/get-started/install)

让Flutter运行在PC上，必须切换到主分支(master channel)的最新版本，启动终端并运行以下命令:

```shell
flutter channel master
flutter upgrade
```

现在运行如下命令：

```shell
flutter doctor 
```

我们可以看到类似这样的输出(根据当前环境输入有所不同)：
![image.png](https://upload-images.jianshu.io/upload_images/453533-4b1a35906cb42ef0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在，我们可以看到设备列表没有显示已连接设备。这是因为默认情况下，Flutter没有启用桌面支持。根据你的系统运行以下命令打开支持：

```shell
flutter config --enable-linux-desktop 
flutter config --enable-macos-desktop
flutter config --enable-windows-desktop
```

请注意，这将为当前终端会话设置环境变量，因此我们将在该终端中执行所有后续步骤。

现在，让我们运行以下命令以确保系统显示。

```shell
flutter devices
```

在输出中，我现在看到Mac已经连接并且可用。

#### 创建应用

用于桌面的Flutter仍然是一个实验特性，这意味着不支持使用"Flutter create"命令创建新的桌面应用程序。因此，惟一的选择是手动下载Demo文件。值得庆幸的是，谷歌的Flutter队已经为我们做到了这一点。

在终端运行这个:

```shell
git clone https://github.com/google/flutter-desktop-embedding.git
cd flutter-desktop-embedding
```

示例目录是一个Flutter应用程序，它包含所有必要的构建脚本，在MacOS、Windows和Linux上运行Flutter都需要这些脚本。如果我们打开示例文件夹的VS代码，我们将能够看到这样的东西:
![image.png](https://upload-images.jianshu.io/upload_images/453533-6523ad9e71ba6fe6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来要做的就是从示例文件夹中运行以下命令，获取项目依赖:

```shell
flutter packages get
```

在我们继续运行我们的应用程序之前，还有最后一步。桌面系统特定的构建工具在默认情况下是不下载的，即使我们第一次运行应用程序时，Flutter也会下载相同的构建工具，但我想确保我们事先就有了它。下载相同的，运行:

```shell
flutter precache --macos
```

根据你的你的操作系统带上参数 --linux,--macos或 --windows。

恭喜你!我们现在已经准备好以桌面应用程序的形式运行我们的Flutter应用程序。

让我们先运行这个应用程序，然后再看一下示例代码。在终端窗口执行:

```shell
flutter run
```

终端输出应该是这样的:
![image.png](https://upload-images.jianshu.io/upload_images/453533-e80a5474f7b336ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在MacOS上运行起来是这样:
![image.png](https://upload-images.jianshu.io/upload_images/453533-a3646ee92ea148e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

嗯！看起来非常简洁美观，终于可以在Linux上运行和MacOS一样漂亮的界面了！

翻译自：[https://medium.com/flutter-community/flutter-for-desktop-create-and-run-a-desktop-application-ebeb1604f1e0](https://medium.com/flutter-community/flutter-for-desktop-create-and-run-a-desktop-application-ebeb1604f1e0)
