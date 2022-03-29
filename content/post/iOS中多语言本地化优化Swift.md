---
title:       "iOS中多语言本地化优化(Swift)"
subtitle:    ""
description: ""
date:        2018-05-14
author:      ""
image:       ""
tags:        ["iOS", "Swift", "多语言本地化"]
categories:  ["iOS" ]
---

> 原文链接：[http://www.cocoachina.com/ios/20170809/20190.html](http://www.cocoachina.com/ios/20170809/20190.html)
> 按照原文操作，发现脚本有问题，各种Google+Baidu狂补Shell+sed知识，最终修复了shell脚本问题(正确脚本在下文中已更正)。

![NSLocalizedString.jpg](https://upload-images.jianshu.io/upload_images/453533-64e9d429a7c41e72.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
本文从提升效率和减少错误两方面对传统的多语言本地化方式进行了优化。

- 传统的方法

- 提升点效率

- 减少些错误

- 自动化万岁
  
  #### 传统的方法
  
  在 Localizable.strings 中写入多种语言的版本，然后使用 NSLocalizedString 进行本地化:

```swift
# en.lproj/Localizable.strings
"login" = "Login";
"logout" = "Logout";
# zh-Hans.lproj/Localizable.strings
"login" = "登录";
"logout" = "退出";
# usage
loginButton.title = NSLocalizedString("login", comment: "login")
logoutButton.title = NSLocalizedString("logout", comment: "logout")
```

这有什么问题呢？ 

繁琐！每次都要写 NSLocalizedString(“xxx”, comment: “xxx”) ，虽然有代码补全，但依然很费时。

#### 提升点效率

直接上代码：

```swift
extension String {
    var localized: String {
        return NSLocalizedString(self, comment: self)
    }
}
```

于是现在的使用方式就变成了：

```swift
loginButton.title = "login".localized
logoutButton.title = "logout".localized
```

这样代码简洁多了，也保留了代码的自解释。 

但，依然还有问题，如果我不小心写成了：

```swift
loginButton.title = "login".localized
logoutButton.title = "loguot".localized
```

编译不会报错，但logoutButton的title却出不来（注意 “loguot”.localized），写错一个字母，抓bug抓好长时间的经历相信很多人都遇到过吧。 

这里涉及到编码中的一个小技巧：不要徒手写同一个需要多次使用的字符串，尽量定义成常量进行调用

####减少些错误
还是直接上代码：

```swift
extension String {
    static var localized_login: String { return "login".localized }
    static var localized_logout: String { return "logout".localized }
}
```

现在用起来就更爽了：

```swift
loginButton.title = .localized_login
logoutButton.title = .localized_logout
```

得益于Xcode代码提示补全的功能，我只需输入”.” “login” 回车，基本就就可以完成输入：
![3435928-77c87961bea59ff2.jpg](https://upload-images.jianshu.io/upload_images/453533-19f5c90854fa85fe.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

乍一看，已经将写字符串时出错的概率降到最低了，但这样又要多写一堆代码，岂不是把之前好不容易提升起来的效率又降低了，再加上万一，我们在写 localized_logout 时还是写成了 “loguot”.localized ，这不是”辛辛苦苦大半年，一朝回到解放前”的节奏？ 

#### 自动化万岁

思路：使用脚本读取 Localizable.strings ，然后输出成我们需要的常量格式。
![image.png](https://upload-images.jianshu.io/upload_images/453533-50664cae8e5538dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Build Phases中新建一个 Run Script，填入以下脚本：

```swift
# Localizable.strings文件路径
localizableFile="${SRCROOT}/${PROJECT_NAME}/Support/en.lproj/Localizable.strings"
# 生成的swift文件路径（根据个人习惯修改）
localizedFile="${SRCROOT}/${PROJECT_NAME}/Source/Utils/LocalizedUtils.swift"
# 将localizable.strings中的文本转为swift格式的常量，存入一个临时文件
sed "s/^\"\([a-z]*\).*/static var localized_\1 : String { return  \"\1\".localized }/g" "${localizableFile}" > "${localizedFile}.tmp"

# 先将localized作为计算属性输出到目标文件
echo -e "import Foundation\n\nextension String {\n  var localized: String { return NSLocalizedString(self, comment: self) }" > "${localizedFile}"

# 再将临时文件中的常量增量输出到目标文件
cat "${localizedFile}.tmp" >> "${localizedFile}"
# 最后增量输出一个"}"到目标文件，完成输出
echo -e "\n}" >> "${localizedFile}"
# 删除临时文件
rm "${localizedFile}.tmp"
```

以上脚本的作用就是将localizable.strings中的内容转换成swift的常量形式，并作为String的extension存储起来，具体步骤看注释。 
(原文中将文本转为Swift格式常量中，错误的将值作为NSLocalizedString的key,原本shell脚本如下：)

```shell
sed "s/^\"/  static var localized_/g" "${localizableFile}" | sed "s/\" = \"/: String { return \"/g" | sed "s/;$/.localized }/g" > "${localizedFile}.tmp" 
```

其中有几点需要注意： 

- sed的用法中，^… 表示以…开头，…$ 表示以…结尾 [参考链接]。

- \> 表示覆盖输出到文件，>> 表示增量输出到文件。

- echo -e 表示将\n作为换行符输出（其他转义字符同效）。

- 将 Run Script 放在 Compile Sources 的上面，这样可以在编译代码前执行，如果出现错误也很容易定位（例如Localizable.strings中行末忘记写分号）。 
  脚本效果：

本地化文件：

```swift
# en.lproj/Localizable.strings
"login" = "Login";
"logout" = "Logout";
```

输出文件：

```swift
# LocalizedUtils.swift
import Foundation
extension String {
  var localized: String { return NSLocalizedString(self, comment: self) }
  static var localized_login: String { return "login".localized }
  static var localized_logout: String { return "logout".localized }
}
```

至此，我们只要在写好Localizable.strings或有修改时 ?+B build一下，就能愉快的使用了。

原文链接：http://www.cocoachina.com/ios/20170809/20190.html
