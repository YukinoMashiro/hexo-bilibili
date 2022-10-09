---
title: ubuntu搭建FTP服务器
author: Yukino
avatar: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/avatar/a26.ico #头像地址
authorLink: /# #头像跳转链接
authorAbout: NULL
authorDesc: NULL
categories: 技术 #public/catagories文章目录
date: 2021-1-19 1:20:00
comments: true #是否开启评论
tags: #文章标签
  - FTP
keywords: FTP #这个暂时没找到用户
description: ubuntu搭建FTP服务器 #首页文章简介
photos: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/article_cover/ #首页的文章的封面图
banner: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/banner/1.jpg #文章详情页的banner
---
Ubuntu 安装 VSFTPD 实现 FTP功能

- 安装VSFTPD

```shell
sudo apt-get install vsftpd
service vsftpd start # 启动vsftpd服务
```

- 新建用户uftp，并设置密码

```shell
sudo adduser userftp #之后的登录用户就是这个了
```

```shell
# 输入对应内容
Retype new UNIX password:
passwd: password updated successfully
Changing the user information for userftp
Enter the new value, or press ENTER for the default
	Full Name []: userftp 
	Room Number []: 1
	Work Phone []: 1
	Home Phone []: 1
	Other []: 1
Is the information correct? [Y/n] y
yukino@yukino:/home$
```
注意：之后userftp作为账户，输入的密码作为登录密码。
`/home/userftp` 已创建好

- 修改配置文件

```shell
sudo vim /etc/vsftpd.conf
# 加入以下代码
userlist_deny=NO
userlist_enable=YES
userlist_file=/etc/allowed_users
seccomp_sandbox=NO
local_root=/home/userftp/
local_enable=YES
write_enable=YES
utf8_filesystem=YES
```

- 创建允许访问的用户列表

```shell
sudo vim /etc/allowed_users
# 加入以下代码
userftp
```

- 检查禁止访问名单

```shell
sudo vim /etc/ftpusers
```

- 重启服务器

```shell
sudo /etc/init.d/vsftpd restart
```

- 访问ftp

```shell
ftp://192.168.245.130
```

注: ftp 默认端口 21