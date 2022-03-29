---
title:       "解决MacOS 10.12破解软件崩溃"
subtitle:    ""
description: ""
date:        2016-10-17
author:      "xtcel"
image:       ""
tags:        ["Mac", "macOS10.12", "崩溃", "破解软件"]
categories:  ["Tech" ]
---

不少用户安装macOS 10.12后，安装网上下载的破解软件，打开后竟然出现程序文件被损坏，要求删除应用的提示，看到这个提示大家本能的理解应该是网上下载的第三方程序出问题，文件被损坏，其实这是苹果阻止用户安装破解软件的一项策略，防止苹果盗版软件的盛行。

![APP打不开](https://s2.loli.net/2022/03/05/CV9cDNAIa3ktziZ.png)

用户可能已经发现，在新版的"安全性与隐私"中已找不到"任何来源"选项，意思是用户只能从AppStore下载或者安装已认证的开发者的程序，从其他网站下载的程序大部分是已经被破解修改重新打包的APP，这些APP的开发者是不受认证的。那么怎么打开任何来源?接来下我就给大家详细讲解.非常简单!

![](https://s2.loli.net/2022/03/05/8pbsGzq1h5lPXE7.png)

#### 打开终端
打开终端,如下图:

![](https://s2.loli.net/2022/03/05/gik7Dlu1sqOnWU2.jpg)
#### 输出命令
然后输入以下命令:
```
sudo spctl --master-disable
```
如下图:
![](https://s2.loli.net/2022/03/05/19rOibWLRPD6GCq.png)

输入后回车，sudo命令需要输入电脑的密码，将你的登录密码输入认证就可以了。
#### 成功啦
然后再重新打开“安全性与隐私”,已经出现并选中"任何来源"了。

![](https://s2.loli.net/2022/03/05/prX9ztn5QdENluV.png)

再次打开你的APP，已经不会再出现文件损坏的提示。

![](https://s2.loli.net/2022/03/05/wYFUjHq4mg8NBLS.png)

APP正常运行，终于可以继续愉快的玩耍了。继续coding....
