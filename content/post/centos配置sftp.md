---
title:       "CentOS 配置 sftp"
subtitle:    ""
description: ""
date:        2019-06-29
author:      "xtcel"
image:       ""
tags:        ["centos", "配置sftp"]
categories:  ["Tech" ]
---

##### 安装依赖
```shell
yum install openssh-server openssl
```

备份
```shell
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
```

##### 修改ssh配置文件
```shell
vi /etc/ssh/sshd_config
```

文末添加

```shell
Subsystem sftp internal-sftp
Match Group sftpgroup
Match User xgwxsftp
X11Forwarding no
AllowTcpForwarding no
ChrootDirectory /data/www/
ForceCommand internal-sftp
```

##### 配置目录权限
```shell
mkdir -p /sftp/sftpuser //-p 表示parents，即递归创建目录
usermod -d /sftp/sftpuser sftpuser // -d 表示修改用户home目录

// 设置Chroot目录权限
chown root:sftpgroup /sftp/sftpuser
chmod 755 /sftp/sftpuser

// 设置sftp用户可以操作的目录
mkdir /sftp/sftpuser/upload
chown sftpuser:sftpgroup /sftp/sftpuser/upload
chmod 755 /sftp/sftpuser/upload
```
##### 重启ssh服务
centos7
``` shell
systemctl restart sshd.service
```

centos6

```shell
service sshd restart
```

##### 参考

SFTP服务配置以及命令/代码操作
https://blog.csdn.net/zhichao_qzc/article/details/80301994

CentOS6.9下sftp配置和scp用法
https://blog.csdn.net/Pipcie/article/details/83545603

CentOS 6配置SFTP
https://www.jianshu.com/p/5025d6de4e09

给sftp创建新用户、默认打开和限制在某个目录
https://www.cnblogs.com/xjnotxj/p/6912471.html
