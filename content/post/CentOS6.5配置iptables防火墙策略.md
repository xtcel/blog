---
title:       "CentOS 6.5 配置iptables防火墙策略"
subtitle:    ""
description: ""
date:        2017-12-30
author:      "xtcel"
image:       ""
tags:        ["CentOS", "防火墙配置"]
categories:  ["Tech" ]
---

#### 配置filter表防火墙

 清除预设表filter中的所有规则链的规则

```shell
 iptables -F
```

清除预设表filter中使用者自定链中的规则

```shell
iptables -X
```

保存iptables配置

```shell
service iptables save
```

重启iptables服务

```shell
service iptables restart
```

查看iptables规则

```shell
iptables -L -n
```

查看iptables规则文件

```shell
cat /etc/sysconfig/iptables
```

设定预设规则

```shell
iptables -P INPUT DROP
iptables -P OUTPUT ACCEPT
iptables -P FORWARD DROP
```

开启22端口

```shell
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

如果OUTPUT设置成DROP需要添加 

```shell
iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT
```

关闭22端口 

```shell
iptables -D INPUT -p tcp --dport 22 -j ACCEPT
```

开启常用端口

```shell
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 3306 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 80 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 3306 -j ACCEPT
iptables -A INPUT -p tcp --dport 20 -j ACCEPT
iptables -A INPUT -p tcp --dport 21 -j ACCEPT
iptables -A INPUT -p tcp --dport 10000 -j ACCEPT
iptables -A INPUT -p tcp --dport 25 -j ACCEPT
iptables -A INPUT -p tcp --dport 110 -j ACCEPT
iptables -A INPUT -p udp --dport 53 -j ACCEPT
```

允许ping

```shell
iptables -A INPUT -p icmp -j ACCEPT
```

如果OUTPUT设置成DROP需要添加

```shell
iptables -A OUTPUT -p icmp -j ACCEPT
```

允许loopback

```shell
iptables -A INPUT -i lo -p all -j ACCEPT
```

允许某个网段的IP远程连接

```shell
iptables -A INPUT -s 192.168.10.0/24 -p tcp --dport 22 -j ACCEPT
```

允许指定网段通过、指定网口通过SSH连接本机

```shell
iptables -A INPUT -i eth0 -p tcp -s 192.168.10.0/24 --dport 22 -m state --state
NEW,ESTABLESHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
iptables -A INPUT -i eth0 -p tcp -s 192.168.10.0/24 --dport 22 -m state --state ESTABLESHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
```

开启转发功能

```shell
iptables -A FORWARD -i eth0 -o eth1 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD -i eth1 -o eh0 -j ACCEPT
```

丢弃坏的TCP包

```shell
iptables -A FORWARD -p TCP ! --syn -m state --state NEW -j DROP
```

处理IP碎片数量,防止攻击,允许每秒100个

```shell
iptables -A FORWARD -f -m limit --limit 100/s --limit-burst 100 -j ACCEPT
```

设置ICMP包过滤,允许每秒1个包,限制触发条件是10个包

```shell
iptables -A FORWARD -p icmp -m limit --limit 1/s --limit-burst 10 -j ACCEPT
```

丢弃非法连接

```shell
iptables -A INPUT -m state --state INVALID -j DROP
iptables -A OUTPUT -m state --state INVALID -j DROP
iptables -A FORWARD -m state --state INVALID -j DROP
```

允许所有已经建立的和相关的连接

```shell
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```

#### 配置NAT表防火墙

查看NAT表规则

```shell
iptables -t nat -L
```

清除NAT规则

```shell
iptables -F -t nat
iptables -X -t nat
iptables -Z -t nat
```

 防止外网用内网IP欺骗 

```shell
iptables -t nat -A PREROUTING -i eth0 -s 10.0.0.0/8 -j DROP
iptables -t nat -A PREROUTING -i eth0 -s 172.16.0.0/12 -j DROP
iptables -t nat -A PREROUTING -i eth0 -s 192.168.0.0/16 -j DROP
```

禁止与某个IP的所有连接

```shell
iptables -t nat -A PREROUTING -d 192.168.10.1 -j DROP
```

禁用80端口

```shell
iptables -t nat -A PREROUTING -p tcp --dport 80 -j DROP
```

禁用某个IP的80端口

```shell
iptables -t nat -A PREROUTING -p tcp --dport 21 -d 192.168.10.1 -j DROP
```

#### 保存iptables文件，重启服务

保存iptables规则

```shell
service iptables save
```

重启iptables服务

```shell
service iptables restart
```
