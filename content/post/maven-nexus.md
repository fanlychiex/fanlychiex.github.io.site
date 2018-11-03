+++
categories = []
date = "2018-09-24T20:42:31+08:00"
description = "maven,nexus,nexus3,私服,部署,发布,安装"
tags = ["maven","notepad"]
title = "Maven发布构件到私服"
draft = false
+++

> 以[Nexus3搭建的Maven私服](https://fanlychiex.github.io/post/nexus3-setup/)为例子，用管理员账户登录系统平台，并在管理控制台的配置面板中创建一个用于发布项目构件的账户（注：在Maven中，所有的依赖、插件、项目构建的输出，都可以称作是构件）。

<!--more-->

#### 创建角色

在Nexus3管理控制台中，创建一个具有`nx-component-upload`和`nx-repository-view-*-*-*`两个权限的角色：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/nexus_component_role.png)

#### 创建用户

在Nexus3管理控制台中，创建一个用作构件部署的账户，并将刚刚创建的角色分配给该用户：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/nexus_component_user.png)

#### settings.xml配置

在maven的用户或全局`settings.xml`配置文件，添加如下代码：

```xml
<servers>
    <server>
        <id>nexus-maven-snapshot</id>
        <username>deployment</username>
        <password>deployment123</password>
    </server>
    <server>
        <id>nexus-maven-releases</id>
        <username>deployment</username>
        <password>deployment123</password>
    </server>
</servers>
```

`<server>`用于配置连接到远程服务器的账户名和账户密码。主要用于发布项目构件到Maven私服的时候，为本地连接远程私服服务器进行权限认证提供所需的用户名和密码。

#### pom.xml配置

在项目的`pom.xml`配置文件中，添加如下代码：

```xml
<distributionManagement>
    <repository>
        <id>nexus-maven-releases</id>
        <url>http://10.10.10.121:8081/repository/maven-releases/</url>
    </repository>
</distributionManagement>
```

注：如果项目是快照版本（项目pom.xml配置文件中version的值含有`*-SNAPSHOT`）则使用`<snapshotRepository>`标签替换`<repository>`标签。

- [x] `<distributionManagement>`用于配置项目构件发布到maven私服的哪个仓库中存储。
- [x] `<id>`的值必须与settings.xml配置文件中`<server>`子标签`<id>`的值匹配，否则在发布构件到maven私服时，会因查找不到账户和密码导致本地连接到远程私服服务器失败（maven构建的时候报`401`错误）。
- [x] `<url>`用于指定当前项目构件发布到maven私服时，具体上传存储到的那个仓库的地址。

#### 部署构件

在命令行中执行命令：

```
$ mvn clean deploy
```

如果部署的构件是一个快照版本，由于存储快照版本的仓库是允许构件重新部署的，因此快照版本的构件在每次发布时都会自动带上一个时间戳标记，以作为区分和历史备份。如图所示：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/nx-snapshot.png)