---
title:       "Ubuntu 配置Nginx+PHP+MySql"
subtitle:    "Ubuntu 配置Nginx+PHP+MySql"
description: "Ubuntu 配置Nginx+PHP+MySql"
date:        2015-08-12
author:      "xtcel"
image:       ""
tags:        ["MySql", "Nginx", "PHP", "Ubuntu", "服务器"]
categories:  ["Tech" ]
---

Nginx 是一个轻量级，以占用系统资源少，运行效率而成为web服务器的后起之秀，国内现在很多大型网站都以使用nginx.
PHP亦是当前最火爆的web服务器脚本语言。
MySql速度快，体积小，作为服务器后台数据库非常受欢迎。
这个组合可以非常适合作为一些小型网站、博客的服务器。

![img](http://o88f31ee3.bkt.clouddn.com/blog/image/jpg/Server.jpg)

#### 一、使用官方PPA安装 Nginx 最新版本

1. 安装python-software-properties，要先安python-software-properties 才能使用 add-apt-repository，我一开始就直接使 用sudo add-apt-repository ppa:nginx/stable命令，导致了add-apt-repository:command not found错误。

   ```
   apt-get install python-software-properties
   ```

2. 添加该源的keyserver，而add-apt-repository 就可以把添加源可添加apt-key的工作全部作了

   ```
   sudo add-apt-repository ppa:nginx/stable
   ```

3. 更新

   ```
   sudo apt-get update
   ```

4. 安装Nginx:

   ```
   sudo apt-get install nginx
   ```

5. Test Nginx：
   启动 Nginx：

   ```
   sudo /etc/init.d/nginx start
   ```

   浏览器浏览运行情况输入：[http://localhost](http://localhost/) ;如果现实”Welcome to nginx!”，表明你的 Nginx 服务器安装成功! 关闭 Nginx：

   ```
   sudo /etc/init.d/nginx stop;
   ```



#### 二、配置安装PHP和MYSQL

1. 安装php和MySQL:

   ```
   sudo apt-get install php5-cli php5-cgi mysql-server php5-mysql
   ```



#### 三、安装配置spawn-fcgi、配置Nginx，配置Nginx和spawn-fcgi配合运行

1. /usr/bin/spawn-fcgi这个文件来管理 FastCgi，它原属于lighttpd这个包里面，但 9.10 后，spawn-fcgi 被分离出来单独成包：

   ```
   sudo apt-get install spawn-fcgi
   ```

2. 在/etc/nginx/fastcgi_params 文件最后,加入一行

   ```
   sudo vi /etc/nginx/fastcgi_params
   ```

   加入此行:

   ```
   fastcgi_param   SCRIPT_FILENAME   $document_root$fastcgi_script_name;
   ```

3. 另外需要在PHP-CGI的配置文件(Ubuntu 上此配置文件位于/etc/php5/cgi/php.ini)中,找到cgi.fix_pathinfo选项(找不到直接添加一行就可以了),修改为:

   ```
   cgi.fix_pathinfo=1;
   ```

   这样php-cgi方能正常使用SCRIPT_FILENAME这个变量.

4. 修改nginx的配置文件：/etc/nginx/sites-available/default 修改主机名：

   ```
   server_name localhost;
   ```

   修改index的一行修改为：

   ```
   index index.php index.html index.htm;
   ```

5. 去除local .~ php 注释，改为如下

   ```
   location ~ .php$ {
   fastcgi_pass 127.0.0.1:9000;
   fastcgi_index index.php;
   fastcgi_param SCRIPT_FILENAME /var/www/nginx-default$fastcgi_script_name;
   include /etc/nginx/fastcgi_params; #包含fastcgi的参数文件地址
   ```

6. 开始fast_cgi进程

   ```
   sudo /usr/bin/spawn-fcgi -a 127.0.0.1 -p 9000 -C 5 -u www-data -g www-data -f /usr/bin/php5-cgi -P /var/run/fastcgi-php.pid
   ```

7. 为了让php-cgi开机自启动：
   cd /etc/init.d cp nginx php-cgi vim php-cgi
   提示：如果打开php文件出现:No input file specified请检查php.ini的配置中 cgi.fix_pathinfo=1



#### 四、测试是否配置好PHP

然后运行rcconf设置php-cgi为开机自启动,创建测试phpinfo：

```
sudo vi /var/www/html/index.php
```

插入如下内容

```
<? php phpinfo(); ?>
```

打开http://localhost/;
如果配置正确，可以看到PHP版本信息页面
