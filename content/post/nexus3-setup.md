+++
categories = []
date = "2018-09-22T09:59:59+08:00"
description = "nexus,nexus3,maven,安装,配置,搭建"
tags = ["nexus","maven","notepad"]
title = "Nexus3搭建Maven私服"
draft = false
+++

> [Sonatype Nexus Repository OSS](https://www.sonatype.com/download-oss-sonatype)自称是`The world's first and only universal repository solution that's FREE to use`。Nexus是目前用来搭建[Apache Maven](http://maven.apache.org/)私服仓库最多的免费开源工具。

<!--more-->

#### 环境

`CentOS7` `Java8` `Nexus3`

#### 下载

[官网](https://www.sonatype.com/download-oss-sonatype)下载所需安装包：[nexus-3.12.1-01-unix.tar.gz](http://download.sonatype.com/nexus/3/nexus-3.12.1-01-unix.tar.gz)。

#### 安装

不建议用`root`账户安装和启动。建议用普通的部署账户安装和启动。

创建一个`nexus3`目录

```
$ mkdir nexus3
```

解压缩到`nexus3`目录

```
$ tar zxvf nexus-3.12.1-01-unix.tar.gz -C nexus3
```

#### 配置（可选）

nexus3/nexus-3.12.1-01/bin/nexus.vmoptions

```
# JVM 参数配置
-Xms256M
-Xmx1024M
-XX:MaxDirectMemorySize=2G
-XX:+UnlockDiagnosticVMOptions
-XX:+UnsyncloadClass
-XX:+LogVMOutput
# 日志文件
-XX:LogFile=../sonatype-work/nexus3/log/jvm.log
-XX:-OmitStackTraceInFastThrow
-Djava.net.preferIPv4Stack=true
-Dkaraf.home=.
-Dkaraf.base=.
-Dkaraf.etc=etc/karaf
-Djava.util.logging.config.file=etc/karaf/java.util.logging.properties
# 仓库数据目录
-Dkaraf.data=../sonatype-work/nexus3
-Djava.io.tmpdir=../sonatype-work/nexus3/tmp
-Dkaraf.startLocalConsole=false
```

#### 运行

前台启动命令

```
$ bin/nexus run
```

后台启动命令

```
$ bin/nexus start
```

停止命令

```
$ bin/nexus stop
```

重启命令

```
$ bin/nexus restart
```

#### 开放防火墙端口

`nexus`服务默认运行在`8081`端口

```
$ firewall-cmd --permanent --add-port=8081/tcp
$ firewall-cmd --reload
```

#### 访问

`http://your_ip:8081`

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/nexus_1.png)

默认账户密码：`admin/admin123`

#### maven仓库

仓库 | 类型 | 描述
---|---|---
maven-central | proxy | 远程中央仓库
maven-releases | hosted | 私库发行仓库
maven-snapshots | hosted | 私库快照仓库
maven-public | group | 仓库组

nexus3自带的 nuget-* 仓库可以删除，nuget是微软.NET开发平台的软件包管理器，这里用不到。

#### 仓库类型

类型 | 描述
---|---
proxy | 可以自主配置使用的远程仓库地址
hosted | 内部项目构件发布的仓库类型
virtual | 虚拟仓库类型（基本不用）
group | 可以自由顺序组合多个仓库使用

#### 创建仓库

- [x] 创建proxy仓库

`Repository-->Repositories-->Create repository-->maven2(proxy)`

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/nexus_proxy_repo.png)

附阿里云中央仓库地址：http://maven.aliyun.com/nexus/content/groups/public/

- [x] 创建第三方构件仓库

`Repository-->Repositories-->Create repository-->maven2(hosted)`

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/nexus_hosted_repo.png)

| 部署策略 | 描述 |
| ---- | ---- |
| Allow redeploy | 允许重新部署。<br>同一个构件同一版本的包可以重复发布多次到此仓库中。 |
| Disable redeploy | 不允许重新部署。<br>即一个构件同一版本的包只可以发布一次到此仓库中。 |
| Read-only | 只读。<br>不允许发布构件到此仓库中。 |

- [x] 配置仓库组

`Repository-->Repositories-->Create repository-->maven2(group)`

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/nexus_public_repo.png)

注：注意仓库的顺序。maven查找依赖时会依次遍历仓库组中的仓库。

#### 创建角色

为第三方构件仓库创建角色：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/nexus_component_role.png)

创建角色的同时可以为当前创建的角色分配权限。

#### 创建用户

为第三方构件仓库创建用户：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/nexus_component_user.png)

创建用户并为创建的用户挂上相应的角色。

#### 上传构件到第三方库

`Browse-->3rd.party-->Upload component`

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/nexus_component_user_upload.png)

`选择上传的JAR包并填写相应的信息`

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/nexus_component_user_uploading.png)

`上传成功后的视图`

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/nexus_component_user_upload_success.png)