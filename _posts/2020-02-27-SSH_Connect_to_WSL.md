---

layout: post
title: 【问题与处理】SSH工具链接Win10子系统WSL
categories: Solve_Problems
description: 问题和处理
keywords: SSH工具,WSL,Ubuntu

---



> 我在微软应用商店下载了Ubuntu 18.04 LTS.但是Windows的命令行太丑,我打算使用SSH工具连接WSL,输入密码一直拒绝连接...
> 查找资料之后解决了这个问题

双击安装WSL完成后生成的图标打开命令行窗口

#### 1.SSH配置过程:

```shell
#先删掉原有的ssh
$ sudo apt-get remove --purge openssh-server

#重新安装ssh服务
$ sudo apt-get install openssh-server ssh  

#编辑sshd_config文件，修改几处配置才能正常使用用户名/密码的方式连接
$ sudo vi /etc/ssh/sshd_config

#需要找到并修改以下几项(其他博客有修改其他项成功的,但是我只改了这两项就OK了)
Port 223  #默认的是22，Windows自己的SSH服务也是的22端口，所以我改成了223
PermitRootLogin yes #默认是PermitRootLogin prohibit-password

#启动ssh服务
sudo service ssh --full-restart
```

#### 2.修改密码(root用户下)

```shell
$ passwd #修改密码
Changing password for user root..
New password: #输入新密码
Retype new password:  #再次确认新密码
passwd: all authentication tokens updated successfully.
```

之后使用SSH工具连接,推荐MobaXterm](https://mobaxterm.mobatek.net/).

#### 3.注意: 

* IP使用127.0.0.1(或者localhost);端口使用你修改的值(我这里是223)

* 不能直接使用命令ssh root@127.0.0.1来连接WSL,因为你修改了端口,而SSH默认使用端口是22
