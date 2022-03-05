---
title:       "阿里重磅开源！首款开源 IDE 研发框架 OpenSumi"
subtitle:    ""
description: "历经3年的开发与打磨，在阿里集团及蚂蚁集团共建小组的努力下，OpenSumi 作为国内首个强定制性、高性能，兼容 VS Code 插件体系的 IDE 研发框架，今天正式对外开源，项目采用使用较为宽松的 MIT 许可协议"
date:        2022-03-04
author:      "xtcel"
image:       ""
tags:        ["OpenSumi", "IDE", “首款开源IDE”, "Sumi"]
categories:  ["Tech" ]
---

历经3年的开发与打磨，在阿里集团及蚂蚁集团共建小组的努力下，OpenSumi 作为国内首个强定制性、高性能，兼容 VS Code 插件体系的 IDE 研发框架，今天正式对外开源，项目采用使用较为宽松的 MIT 许可协议。

![](http://qn.xtcel.com/posts/opensumi/01.png)

#### OpenSumi 是什么？

[OpenSumi](https://github.com/opensumi/core) 是一款面向垂直领域，低门槛、高性能、高定制性的双端（Web 及 Electron)IDE 研发框架，基于 TypeScript+React 进行编码，实现了包含资源管理器、编辑器、调试、Git 面板、搜索 板等核新功能模块。开发者只要基于起步项目进行简单配置，就可以快速搭建属于自己的本地或云端 IDE 产品。

框架自身兼容 VS Code 插件生态，主流 VS Code 插件均可⽆缝在基于 OpenSumi 研发的产品中运行。同时，OpenSumi 为开发者提供了多种低成本、⾼定制的视图定制能力，能满足 IDE 场景下绝大多数视图定制场景。

![](http://qn.xtcel.com/posts/opensumi/02.png)

#### 再造一个VSCode ？

OpenSumi的技术选型和架构都趋同与VSCode，并且还兼容VSCode的插件，这让人们不经意的想到阿里是想再造一个VSCode吗？其实在OpenSumi的Demo中，确实有一个和VSCode几乎一样的项目。笔者尝试下载安装试用了一下，和VSCode几乎没有差别。但这并不是OpenSumi的全部，从这个Demo中我们可以窥探出OpenSumi的基础能力，但阿里还有更大的野心。大部分程序员应该或多或少用过Jetbrains系列的IDE，这家捷克软件公司开发的IDE几乎覆盖了所有开发技术栈，有[IntelliJ IDEA](https://www.jetbrains.com/idea/)、[WebStorm](https://www.jetbrains.com/webstorm/)、[GoLand](https://www.jetbrains.com/go/)、[PyCharm](https://www.jetbrains.com/pycharm/)、[PhpStorm](https://www.jetbrains.com/phpstorm/)等等，包括大名鼎鼎的Android Studio也是采用他们的内核。OpenSumi就是要对标Jetbrains，希望借助开源的力量来创建一个基于OpenSumi的IDE产品矩阵。

![](http://qn.xtcel.com/posts/opensumi/03.png)

#### OpenSumi 有什么优势？

我觉得OpenSumi的最大优势还是Open开源，因为开源可以吸引很多优秀的人才一起开发。其他公司也可以基于OpenSumi去开发定制自己的IDE，将国内的IDE市场激活。

产品力方面阿里的研发实力毋庸置疑，OpenSumi也经过几年的沉淀，已经针对小程序场景研发了支付宝小程序开发者工具 以及 淘宝小程序开发者工具。另外随着Cloud IDE越来越火，云端一体化研发链路也为OpenSumi带来更多可能。
