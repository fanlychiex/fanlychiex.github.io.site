+++
categories = []
date = "2018-09-24T21:22:31+08:00"
description = "maven,mirror,mirrors,镜像"
tags = ["maven","notepad"]
title = "Maven镜像"
draft = false
+++

> 如果一个仓库X可以提供仓库Y存储的所有构件，那么就可以称仓库X是仓库Y的一个镜像。由于地理位置等原因，国内网络连接Maven官方的中央仓库网速一般较慢或时常出现网络不稳定的状态，从而导致项目在构建所需的时间较长或失败。使用镜像的好处就是，它往往能提供比中央仓库更快的服务（通常选择地理位置上与自己较近且口碑较好的镜像）, 从而提高下载速度, 最终达到提高项目构建效率的目的。

<!--more-->

在maven的用户或全局的`settings.xml`配置文件中，找到在`<mirrors>`配置：

```xml
<mirrors>
    <mirror>
        <id>aliyun-maven-repo</id>
        <!-- 阿里云公共镜像 -->
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        <!-- 匹配Maven中央仓库, 该ID在超POM中定义的 -->
        <mirrorOf>central</mirrorOf>
    </mirror>
</mirrors>
```

`<mirror>`用于配置一个镜像。`<id>`必须是唯一的。`<url>`是镜像的地址。`<mirrorOf>`用于匹配Maven仓库（`<repository>`）的ID。常见的匹配规则有：

| 通配符 | 描述 |
| ---- | ---- |
| * | 匹配所有的Maven仓库ID |
| repo1,repo2 | 只匹配ID为repo1或repo2的仓库 |
| external:* | 匹配除本地仓库之外的所有仓库 |
| *,!repo1 | 匹配除ID为repo1之外的所有仓库 |

Maven在构建项目的时候，每当一个构件在本地仓库中查找不到时，它就会去远程中央仓库（central，[Super POM](https://fanlychiex.github.io/post/maven-super-pom)中声明使用的远程仓库地址）中去下载。如果某个镜像的`<mirrorOf>`匹配到该仓库（如上面的aliyun-maven-repo镜像），则去这个仓库下载构件的请求就会被该镜像拦截掉，并将此次请求转发到该镜像配置的地址中去下载。本地所有访问远程仓库的请求都被转换成对镜像地址的请求，这就是镜像的作用和意义。

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/mvn_mirror.png)