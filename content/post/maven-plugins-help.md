+++
categories = []
date = "2018-09-24T20:22:31+08:00"
description = "maven,help,mavenhelp,帮助,插件"
tags = ["maven","notepad"]
title = "Maven Help Plugin"
draft = false
+++

> [Maven Help Plugin](http://maven.apache.org/plugins/maven-help-plugin)可以用来查看相关项目或系统的帮助信息，也可以用来查看其它插件的帮助信息，如插件的目标、参数、使用要求等。

<!--more-->

### 环境

`Windows 10` `Maven 3.5.4`

### help:system

用于查看操作系统属性和环境变量的详细信息。

```
mvn help:system
```

如果输出结果行数太多导致无法查看完整的内容，可以将执行结果输出到外部文件中。

```
mvn help:system > mylog.txt
```

支持使用文件的相对路径和绝对路径，如果是相对路径，则为cmd当前所指示的路径。以下同。

### help:effective-pom

用于查看当前构建的maven模块（项目）POM文件的有效内容。这些有效内容指的不仅仅是当前项目POM文件中的内容，它还包含继承父模块的内容（如果有父模块）以及还会合并Super Pom文件中的内容一起输出。

```
mvn help:effective-pom
```

### help:active-profiles

用于查看当前构建的maven模块（项目）POM文件的有效Profile（其中包括项目POM文件中激活的Profile和用户settings.xml以及全局settings.xml文件中激活的Profile）。

```
mvn help:active-profiles
```

### help:effective-settings

用于查看当前构建的maven模块（项目）POM文件的有效设置。这些有效的设置包含和合并了用户settings.xml以及全局settings.xml文件中的设置。

```
mvn help:effective-settings
```

### help:describe

用于查看插件有关的详细帮助信息。可以通过参数`-Dplugin`或`-DgroupId -DartifactId -Dversion`（其中`-Dversion`参数是可选的）来指定想要研究的插件。

```
mvn help:describe -DgroupId=org.apache.maven.plugins -DartifactId=maven-help-plugin
```

或者：

```
mvn help:describe -Dplugin=help
```

参数`-Dplugin`的值是插件的名称，可参照官方文档[Maven Plugins](http://maven.apache.org/plugins/index.html)。

### help:evaluate

用于评估当前构建的maven模块（项目）POM文件中的maven表达式的值。

```
mvn help:evaluate
... ...
[INFO] Enter the Maven expression i.e. ${project.groupId} or 0 to exit?:
${project.version}
[INFO]
0.0.1-SNAPSHOT
[INFO] Enter the Maven expression i.e. ${project.groupId} or 0 to exit?:
```