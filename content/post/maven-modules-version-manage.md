+++
categories = []
date = "2018-09-25T22:22:31+08:00"
description = "maven,multiple,multi,modules,module,versions,plugin,多模块,项目,版本,控制,管理"
tags = ["maven","notepad"]
title = "Maven多模块项目版本管理"
draft = false
+++

> 如果一个项目要迭代一个新的版本，特别是对于一些子模块数量较多的项目，那就需要手工的一个一个的去修改各个模块POM中的版本号信息，这显然不是一个最好的做法。[Versions Maven Plugin](https://www.mojohaus.org/versions-maven-plugin/index.html)可用于管理控制Maven多模块项目POM中的版本号信息。

<!--more-->

#### [versions:set](https://www.mojohaus.org/versions-maven-plugin/set-mojo.html)

用于设置当前项目的版本。如果当前项目是一个父模块项目，则修改会传播给所有的子模块项目。

```
$ mvn versions:set -DnewVersion=0.0.2-SNAPSHOT
```

在项目`pom.xml`目录下执行此命令，以更新项目POM的版本号信息。以此同时会在此目录下生成一个`pom.xml.versionsBackup`文件，该文件是执行更新项目版本号信息前，对当前项目`pom.xml`文件的一个备份文件。

#### versions:revert

恢复项目`pom.xml`文件的上一个版本号信息。回滚的过程依赖生成的`pom.xml.versionsBackup`文件。如果恢复成功，插件会自动删除`pom.xml.versionsBackup`文件。

#### versions:commit

提交项目版本号信息的修改。提交后插件会自动删除`pom.xml.versionsBackup`文件。

#### 示例项目源码

[https://github.com/fanlychie/maven-multiple-modules-samples/tree/master/hierachical-project-layout/maven-multiple-modules-sample](https://github.com/fanlychie/maven-multiple-modules-samples/tree/master/hierachical-project-layout/maven-multiple-modules-sample)