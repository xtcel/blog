---
title:       "使用frp将内网网站发布到外网"
subtitle:    ""
description: "使用frp将内网网站发布到外网"
date:        2020-06-04
author:      "xtcel"
image:       "img/home-bg-ocean.jpg"
tags:        ["frp", " 内网穿透"]
categories:  ["Tech" ]
---
#### 为什么需要内网穿透？
我在自己的电脑上搭建了Web网站，如何让他人在外网直接访问到Web内容呢？
通常，为了能从外网访问Web网站，需要将Web网站挂载到云服务器上，那有什么方法可以在外网访问到本地网站呢？
有以下几种方法：
- 拉一条专线，有配公网IP地址（土豪你可以不用看这个帖子）
- 使用花生壳软件，免费限制时长不好用，收费有点贵
- ngrok工具，免费但不能固定域名，重启后域名变化
- frp完全免费，可以配置固定域名，但需一台外网服务器

#### What's frp ?
frp 是一个可用于内网穿透的高性能的反向代理应用，支持 tcp, udp 协议，为 http 和 https 应用协议提供了额外的能力，且尝试性支持了点对点穿透。
![frp](https://s2.loli.net/2024/06/21/eU3fspGYaZ9tVLA.png)

#### 服务器配置
使用SSH登陆服务器，通过```arch```命令查看服务器架构，根据自己的云服务器架构[下载](https://github.com/fatedier/frp/releases)对应的Frp软件包。
```
wget https://github.com/fatedier/frp/releases/download/v0.30.0/frp_0.30.0_freebsd_amd64.tar.gz
```
使用tar命令解压
```
tar -zxvf frp_0.30.0_freebsd_amd64.tar.gz
```
解压后cd进入frp文件目录
1.修改 frps.ini 文件
```
# frps.ini
[common]
bind_port = 7000
vhost_http_port = 8080
```
bind_port是frp内部通信的端口，vhost_http_port是http访问端口，这里我们设置为8080，一般云服务器80端口已经被占用，如果没有在服务器上挂网站也可以直接使用80端口。
2.启动 frps：
```
./frps -c ./frps.ini
```
如果看到屏幕输出这样一段内容，即表示运行正常，如果出现错误提示，请检查端口是否被其他程序占用。
```
2019/01/12 15:22:39 [I] [service.go:130] frps tcp listen on 0.0.0.0:7000
2019/01/12 15:22:39 [I] [service.go:172] http service listen on 0.0.0.0:10080
2019/01/12 15:22:39 [I] [service.go:193] https service listen on 0.0.0.0:10443
2019/01/12 15:22:39 [I] [service.go:216] Dashboard listen on 0.0.0.0:7500
2019/01/12 15:22:39 [I] [root.go:210] Start frps success
```
至此，我们的服务端仅运行在前台，如果Ctrl+C停止或者关闭SSH窗口后，frps均会停止运行，因而我们使用 [nohup命令](https://ehlxr.me/2017/01/18/Linux-%E7%9A%84-nohup-%E5%91%BD%E4%BB%A4%E7%9A%84%E7%94%A8%E6%B3%95/ "nohup命令")将其运行在后台。
```
nohup ./frps -c frps.ini &
```
###### 注意
- 因为需要使用7000端口和8080端口，需要配置防火墙开放端口。并且配置云服务器的安全组策略。否则客户端会连接不上服务器端对应端口出现connect time out.
#### 客户端配置
同样我们需要在本机电脑下载frp软件包，根据自己的电脑系统配置选择下载。我使用的是Mac，我将软件下载放置在用户目录下，并且重命名文件夹为frp方便后期查找。
1.修改 frpc.ini 文件，假设 frps 所在的服务器的 IP 为 x.x.x.x，local_port 为本地机器上 web 服务对应的端口, 绑定自定义域名 www.yourdomain.com:
```
# frpc.ini
[common]
server_addr = x.x.x.x
server_port = 7000

[web]
type = http
local_port = 80
custom_domains = www.yourdomain.com
```
2.启动 frpc：
```
./frpc -c ./frpc.ini
```
3.将 www.yourdomain.com 的域名 A 记录解析到 IP x.x.x.x，如果服务器已经有对应的域名，也可以将 CNAME 记录解析到服务器原先的域名。
4.通过浏览器访问 http://www.yourdomain.com:8080 即可访问到处于内网机器上的 web 服务。
至此，你已经可以通过固定域名访问你的内网网站了!
#### 总结
frps和frpc通过7000端口进行tcp通信，将外网的8080端口映射到内网80端口从而实现将内网网站发布到外网。
