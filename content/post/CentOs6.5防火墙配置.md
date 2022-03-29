---
title:       "CentOS 6.5 防火墙配置"
subtitle:    ""
description: ""
date:        2017-12-31
author:      "xtcel"
image:       ""
tags:        ["CentOS", "防火墙配置"]
categories:  ["TECH" ]
---

阿里的centos服务器，6阿里的centos6默认是不安装防火墙的（不知道啥时候改的，记忆中是一直存在防火墙的）

创建iptables配置文件

iptables -P OUTPUT ACCEPT

/etc/init.d/iptables save

[![输入图片说明](https://s2.loli.net/2022/03/07/7OZaXLq4i1FTKMA.png "在这里输入图片标题")](https://static.oschina.net/uploads/img/201701/06114040_30t2.png) 

搜索配置文件cat /etc/sysconfig/iptables

[![输入图片说明](https://s2.loli.net/2022/03/07/QGtqUI5nygXD9cu.png "在这里输入图片标题")](https://static.oschina.net/uploads/img/201701/06114117_PJhS.png) 

修改防火墙配置文件，并开放端口

[![输入图片说明](https://s2.loli.net/2022/03/07/xqGSmDbU3vzJtgf.png "在这里输入图片标题")](https://static.oschina.net/uploads/img/201701/06114128_7OGC.png) 

重启防火墙/etc/init.d/iptables restart

[![输入图片说明](https://s2.loli.net/2022/03/07/dUHJ9XczLQqKMNP.png "在这里输入图片标题")](https://static.oschina.net/uploads/img/201701/06114154_44Yo.png) 

临时生效，重启后复原

开启： `service iptables start `

关闭： `service iptables stop`

2） 永久性生效，重启后不会复原

开启： `chkconfig iptables on `

关闭： `chkconfig iptables off`
