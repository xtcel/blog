---
title:       "Linux查找大文件并清理"
subtitle:    ""
description: "du -m --max-depth=1或du -h --max-depth=1du：用于统计linux中文件或目录所占磁盘空间的大小"
date:        2017-12-06 12:08
author:      "xtcel"
image:       ""
tags:        ["Linux"]
categories:  ["Tech" ]
---

#### 发现问题

测试服后台突然打不开，查看log上面写着文件写入失败，以为是目录权限问题，修改权限后还是不行。（之前因为权限问题，不能写入，修改权限后就可以了）
后来无意中发现FTP上传文件大小为0，Google后好多人说可能是磁盘爆满问题。使用

```shell
df -h
```

发现磁盘占用100%。

#### 查找大文件

du -m --max-depth=1或du -h --max-depth=1du：用于统计linux中文件或目录所占磁盘空间的大小，du参数
-m：以M为单位展示查询结果
-h：以K、M、G为单位展示查询结果，提高信息可读性
--max-depth=1：其中，数字“1”是指查询结果中最多显示的目录层数，这里指最多显示一层目录。

```shell
du -m --max-depth=1
du -h --max-depth=1du
```

最后发现其中两个项目的log文件巨大，一个50+G，一个17+G,直接

```shell
rm -rf 
```

删除文件。
重新访问测试后台，进入成功 [Yeah!]
