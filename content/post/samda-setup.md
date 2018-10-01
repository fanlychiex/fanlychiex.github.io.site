+++
categories = []
date = "2018-09-23T19:59:59+08:00"
description = "samda,文件共享,linux,安装,配置,链接,访问"
tags = ["samda","notepad"]
title = "Samda搭建文件共享服务"
draft = false
+++

> [Samba](https://www.samba.org)是一套使用SMB(Server Message Block)协议的应用程序，通过支持这个协议，Samba允许Linux服务器与Windows系统之间进行通信，使跨平台的互访成为可能。Samba采用C/S模式，其工作机制是让NetBIOS( Windows网上邻居的通信协议)和SMB两个协议运行于TCP/IP通信协议之上，并且用NetBEUI协议让Windows在“网上邻居”中能浏览Linux服务器。

<!--more-->

#### 环境

`CentOS7`

### 安装

```
# yum -y install samba samba-client samba-winbind-clients.x86_64 cifs-utils.x86_64
```

### 配置文件

```
# vim /etc/samba/smb.conf
```

添加：

```
[共享文件夹名称]
        comment = 共享文件夹描述
        path = 共享文件夹路径
        public = No
        write list = developer
        writable = No
```

* `path`：为路径地址，需要先创建此目录，777权限
* `write list`：对该目录具有写权限的用户列表，多个用英文逗号分隔

完整的配置：

```
[global]
        workgroup = SAMBA
        security = user
        passdb backend = tdbsam
        printing = cups
        printcap name = cups
        load printers = yes
        cups options = raw
[homes]
        comment = Home Directories
        valid users = %S，%D%w%S
        browseable = No
        read only = No
        inherit acls = Yes
[printers]
        comment = All Printers
        path = /var/tmp
        printable = Yes
        create mask = 0600
        browseable = No
[print$]
        comment = Printer Drivers
        path = /var/lib/samba/drivers
        write list = @printadmin root
        force group = @printadmin
        create mask = 0664
        directory mask = 0775
# 所有samda用户都有读写权限；[]里面的文字是最终显示此共享文件夹的名字，可以自定义
[共享文件柜1]
        comment = Share Directories
        path = /data/share/share1
        public = No
        writable = Yes
        printable = No
# 只有smb2用户有写权限，其它samda用户只有读权限
[共享文件柜2]
        comment = Share Directories
        path = /data/share/share2
        public = No
        write list = smb2
        writable = No
```

### 防火墙

开放服务端口

```
# firewall-cmd --permanent --add-port=137/tcp
# firewall-cmd --permanent --add-port=138/tcp
# firewall-cmd --permanent --add-port=139/tcp
# firewall-cmd --permanent --add-port=445/tcp
# firewall-cmd --reload
```

### 创建用户

先创建系统用户，或者使用系统当前已有的账户

```
# useradd my_usr_name
```

```
# psswd my_usr_name
```

添加为samba访问账户

```
# smbpasswd -a my_usr_name
```

### 启动

```
# service smb start
```

```
# systemctl start smb
```

### 重启

```
# service smb restart
```

```
# systemctl restart smb
```

### 停止

```
# service smb stop
```

```
# systemctl stop smb
```

### 查看进程

```
$ ps -ef|grep smb
```

### 查看用户

```
# pdbedit -L
```

### 删除用户

```
# smbpasswd -x my_usr_name
```

### 访问文件共享服务

以Windows操作系统为例，按快捷键WIN+R，输入文件共享服务IP地址`\\10.10.XX.125`，回车

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/samba_cmd.png)

接着系统会弹出校验网络凭证的对话框，输入账户名和密码，回车

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/samba_passwd.png)

最终的效果图

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/samba_preview.png)

### 更换文件共享服务的登陆账号

以Windows操作系统为例，由于第一次登陆成功后，操作系统会记住你登陆的账户和密码，以致下次访问时不需要再次登陆。如果想要更换登陆的账户，首先需要清除当前登陆的账户信息。按快捷键WIN+R，输入`control userpasswords2`，回车

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/samba_upasswd.png)

在弹出的对话框中选择高级

<span>![](https://raw.githubusercontent.com/fanlychie/mdimg/master/samba_update1.png)</span>

选择管理密码

<span>![](https://raw.githubusercontent.com/fanlychie/mdimg/master/samba_update2.png)</span>

删除登陆凭证

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/samba_update3.png)

在`cmd`中执行以下命令，清除网络共享缓存

```
net use * /d /y
```

然后重新链接文件共享服务就会弹出校验网络凭证的对话框，输入新的登陆账户和密码登陆即可。