---
title:       "Mac 上使用 Python 修改 .Plist 文件"
subtitle:    ""
description: ""
date:        2018-03-05
author:      "xtcel"
image:       ""
tags:        ["Mac", "Python"]
categories:  ["Tech" ]
---

### 起因

今天遇到一个需要批量处理写入.Plist文件的需要，一开始想直接手动写入，考虑的内容比较多，手写估计要1个小时，于是想是不是可以用程序员的思维去解决这个问题，怎么解决，写个脚本呗！程序员的天职应该就是偷懒，不会偷懒的程序员不是好程序员。其实之前已经有写过一个类似的脚本，是使用oc写的。使用oc比较自然直接，语言也比较熟悉，但是感觉这么一个小问题，还要创建一个iOS工程太麻烦，而且拷贝文件也很不方便，需要到模拟器的沙盒里面去找文件。

那用啥呢？首先我想到的便是最近火的不要不要的脚本语言Python，Python非常简单，有很多语法糖，可以快速的构建出强大的功能。

### Code

```python
#!/usr/bin/env python
#coding=utf-8

import os,sys
reload(sys) 
sys.setdefaultencoding('utf-8') 

base = '/Users/XiaoYao/Desktop/'

u = u"抱抱"

for index in range(25):
    os.system('/usr/libexec/PlistBuddy -c "Set :%s:%d hug_0%02d" BigGiftList.plist'%(u, index, index+1))
```

### 心得

##### 编码问题

因为要使用中文字符串，必须在开头加上
```#coding=utf-8```
否则会报错，
并且需要重载系统默认编码

```python
import os,sys
reload(sys) 
sys.setdefaultencoding('utf-8')
```

##### PlistBuddy

PlistBuddy是Mac自带的专门解析plist的小工具

- 打印plist
  
  ```shell
  /usr/libexec/PlistBuddy -c "print" info.plist
  ```
- 添加字段
  
  ```shell
  /usr/libexec/PlistBuddy -c 'Add :Version string 1.0' info.plist
  ```
  
  # 先添加key值
  
  ```shell
  /usr/libexec/PlistBuddy -c 'Add :Application array' info.plist
  ```
  
  ```shell
  # 添加value值
  yans67deMacBook-Pro:needfiles huangyg$ /usr/libexec/PlistBuddy -c 'Add :Application: string app1' info.plist
  yans67deMacBook-Pro:needfiles huangyg$ /usr/libexec/PlistBuddy -c 'Add :Application: string app2' info.plist
  yans67deMacBook-Pro:needfiles huangyg$ /usr/libexec/PlistBuddy -c 'Add :Application: string app3' info.plist
  ```
- 删除
  
  ```shell
  /usr/libexec/PlistBuddy -c 'Delete :Version' info.plist
  ```
- 修改
  
  ```shell
  /usr/libexec/PlistBuddy -c 'Set :Application:1 string "thi is app1"' info.plist
  ```
- 合并
  
  ```shell
  # 将A.plist 合并到 B.plist中
  /usr/libexec/PlistBuddy -c 'Merge A.plist'  B.plist
  ```

参考：
链接：https://www.jianshu.com/p/2167f755c47e
