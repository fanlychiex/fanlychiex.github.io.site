+++
categories = []
date = "2018-09-25T21:22:31+08:00"
description = "maven,multiple,multi,modules,module,project,多模块,项目"
tags = ["maven","notepad"]
title = "Maven多模块项目"
draft = false
+++

> 以模块的形式来组织项目，可以使得项目的组织结构更加清晰更易维护，每个模块都可以进行高内聚和独立部署。模块之间的依赖关系可以自由进行组合，以提高软件组件的重用，同时各模块之间能够实现松耦合。

<!--more-->

#### 水平项目布局

水平项目布局（Flat Project Layout）指的是父模块项目与子模块项目处于同一级目录下。

```html
maven-multiple-modules-project  // 项目根目录
  |
  |— parent-module                  // 父模块项目
  |      |— pom.xml (pom)
  |
  |— child1-module                  // 子模块项目
  |      |— src
  |      |— pom.xml (jar)
  |
  |— child2-module                  // 子模块项目
  |      |— src
  |      |— pom.xml (jar)
  |
  |— child3-module                  // 子模块项目
  |      |— src
  |      |— pom.xml (war)
  |
```

#### 分层项目布局

分层项目布局（Hierachical Project Layout）指的是父模块项目作为父目录，该目录下包含了所有的子模块项目。

```html
maven-multiple-modules-project  // 父模块项目
  |
  |— pom.xml (pom)
  |
  |— child1-module                  // 子模块项目
  |      |— src
  |      |— pom.xml (jar)
  |
  |— child2-module                  // 子模块项目
  |      |— src
  |      |— pom.xml (jar)
  |
  |— child3-module                  // 子模块项目
  |      |— src
  |      |— pom.xml (war)
  |
```

#### 项目布局的差异

水平和分层结构布局的最主要差异在于父模块`pom.xml`文件是否与子模块处于同一级目录中。分层项目布局父模块`pom.xml`文件与子模块处在同一级目录中，而水平项目布局的方式是处在不同级目录。在多模块项目结构中，当父模块`pom.xml`文件与子模块处在同一级目录时，它能够给我们带来许多方便之处。如在父模块POM文件中声明子模块时，分层项目布局的声明方式为：

```xml
<modules>
    <module>child1-module</module>
    <module>child2-module</module>
    <module>child3-module</module>
</modules>
```

而水平项目布局的声明方式（因其父模块POM文件与子模块处在不同级的目录中）：

```xml
<modules>
    <module>../child1-module</module>
    <module>../child2-module</module>
    <module>../child3-module</module>
</modules>
```

显然第一种方式要比第二种更加简洁。总的来说，推荐使用分层项目布局的方式来组织项目的结构。

#### 创建分层结构布局的项目

demo源码：[https://github.com/fanlychie/maven-multiple-modules-samples/tree/master/hierachical-project-layout/maven-multiple-modules-sample](https://github.com/fanlychie/maven-multiple-modules-samples/tree/master/hierachical-project-layout/maven-multiple-modules-sample)

```html
maven-multiple-modules-project  // 父模块项目
  |
  |— pom.xml (pom)
  |
  |— user-facade                    // 子模块项目
  |      |— src
  |      |— pom.xml (jar)
  |
  |— user-service                   // 子模块项目
  |      |— src
  |      |— pom.xml (jar)
  |
  |— blog-web                       // 子模块项目
  |      |— src
  |      |— pom.xml (war)
  |
```

创建父模块项目`maven-multiple-modules-sample`：

```
mvn archetype:generate -DgroupId=org.fanlychie -DartifactId=maven-multiple-modules-sample -Dversion=0.0.1-SNAPSHOT
```

进入父模块目录：

```
cd maven-multiple-modules-sample
```

删除父模块的`src`目录（父模块是一个没有源代码的项目，因此不需要`src`目录）：

```
rd /s/q src
```

父模块项目的打包类型必须为`pom`，修改`pom.xml`的打包类型如下：

```xml
<packaging>pom</packaging>
```

创建子模块项目`user-facade`：

```
mvn archetype:generate -DgroupId=org.fanlychie -DartifactId=user-facade -Dversion=0.0.1-SNAPSHOT
```

子模块`pom.xml`文件中会自动生成其父模块信息：

```
<parent>
    <groupId>org.fanlychie</groupId>
    <artifactId>maven-multiple-modules-sample</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</parent>
```

创建子模块项目`user-service`

```
mvn archetype:generate -DgroupId=org.fanlychie -DartifactId=user-service -Dversion=0.0.1-SNAPSHOT
```

创建子模块项目`blog-web`：

```
mvn archetype:generate -DgroupId=org.fanlychie -DartifactId=blog-web -Dversion=0.0.1-SNAPSHOT -DarchetypeArtifactId=maven-archetype-webapp
```

在父模块POM文件中使用`<modules>`声明其子模块：

```xml
<!-- modules:
     声明子模块。模块间的先后顺序是没有要求的，maven不需要开发者考虑模块间的依赖关系，
     maven在构建项目时，它会对模块自动进行拓扑排序并确保在依赖模块之前构建依赖的模块项目。
     module:
     它的值通常是子模块项目相对于父模块POM文件的相对路径的名称。
-->
<modules>
    <module>user-facade</module>
    <module>user-service</module>
    <module>blog-web</module>
</modules>
```

将项目导入`IntelliJ IDEA`（主流的JAVA IDE开发工具）。菜单：`File --> Open...`选择父模块项目：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/m_m_m_p_1.png)

项目导入到`IntelliJ IDEA`后，`IntelliJ IDEA`能够识别并自动将各个模块转换成maven项目。如图示：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/m_m_m_p_2.png)

在命令行中执行打包项目的命令，如在`cmd`中到父模块根目录下执行：

```
$ mvn clean -Dmaven.test.skip package
```

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/m_m_m_p_3.png)

可以看到，maven在构建多模块项目时，它会根据模块之间的依赖关系，整理出一个新的构建顺序，然后再按这个顺序来构建各个模块。构建完成之后，会在各个子模块项目的`target`目录下生成输出相对应的`jar`或`war`软件包。

在`IntelliJ IDEA`中运行web项目：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/m_m_m_p_4.png)

文档传送门：[http://maven.apache.org/plugins/maven-eclipse-plugin/reactor.html](http://maven.apache.org/plugins/maven-eclipse-plugin/reactor.html)

#### 创建水平结构布局的项目

demo源码：[https://github.com/fanlychie/maven-multiple-modules-samples/tree/master/flat-project-layout/maven-multiple-modules-sample](https://github.com/fanlychie/maven-multiple-modules-samples/tree/master/flat-project-layout/maven-multiple-modules-sample)

```html
maven-multiple-modules-sample   // 项目根目录
  |
  |— parent-module                  // 父模块项目
  |      |— pom.xml (pom)
  |
  |— user-facade                    // 子模块项目, 用户接口层
  |      |— src
  |      |— pom.xml (jar)
  |
  |— user-service                   // 子模块项目, 用户业务层
  |      |— src
  |      |— pom.xml (jar)
  |
  |— blog-web                       // 子模块项目, 访问控制层
  |      |— src
  |      |— pom.xml (war)
  |
```

按分层结构布局方式创建多模块项目，然后在项目根目录`maven-multiple-modules-sample`下创建父模块目录`parent-module`，并将`maven-multiple-modules-sample/pom.xml`文件移动到`parent-module`文件中。修改`parent-module/pom.xml`（`<artifactId>`、`<name>`、`<modules>`）配置如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.fanlychie</groupId>
    <artifactId>parent-module</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>pom</packaging>
    <name>parent-module</name>
    <url>http://maven.apache.org</url>
    <!-- 子模块的相对路径（相对父模块pom.xml的路径） -->
    <modules>
        <module>../user-facade</module>
        <module>../user-service</module>
        <module>../blog-web</module>
    </modules>
</project>
```

子模块项目`pom.xml`配置文件中继承父模块部分的配置修改为：

```xml
<parent>
    <groupId>org.fanlychie</groupId>
    <artifactId>parent-module</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <!-- 用于指定父模块POM的相对路径。它的默认值是"../" -->
    <relativePath>../parent-module</relativePath>
</parent>
```

子模块POM继承父模块POM，在子模块POM配置中`<relativePath>`用于指定其父模块的POM文件的相对路径。如果`<relativePath>`配置的值是一个目录，则默认寻找该目录下的`/pom.xml`文件。即等效于：

```xml
<parent>
    <groupId>org.fanlychie</groupId>
    <artifactId>parent-module</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <!-- 用于指定父模块POM的相对路径。它的默认值是"../" -->
    <relativePath>../parent-module/pom.xml</relativePath>
</parent>
```

将项目导入`IntelliJ IDEA`。菜单：`File --> Open...`选择项目的根目录：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/m_m_m_p_f_1.png)

导入后是一个普通文件目录，`IntelliJ IDEA`不能够识别出是maven项目，如图示：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/m_m_m_p_f_2.png)

接下来需要将项目转换为maven项目，菜单：`File --> New --> Module from Existing Sources...`

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/maven_m_p3.png)

仍然选到项目的根目录：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/m_m_m_p_f_3.png)

选择maven，点击下一步：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/m_m_m_p_f_4.png)

勾选复选框`Search for projects recursively`递归搜索项目：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/m_m_m_p_f_5.png)

选择maven构建环境：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/m_m_m_p_f_6.png)

直接下一步：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/m_m_m_p_f_7.png)

选择编译使用的JDK版本，点击完成按钮：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/m_m_m_p_f_8.png)

如果项目还报找不到JDK的问题，按快捷键`Ctrl+Alt+Shift+S`重新选择JDK：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/m_m_m_p_f_9.png)

#### 子模块可继承的POM元素

父模块项目POM配置文件中的大部分元素信息能够被子模块所继承，这些元素有：

| 元素 | 描述 |
| ---- | ---- |
| **groupId** | 项目组ID |
| **version** | 项目版本号 |
| description | 项目描述信息 |
| url | 项目的地址 |
| inceptionYear | 项目成立年份 |
| organization | 项目的组织信息 |
| licenses | 项目许可证 |
| developers | 项目开发者信息 |
| contributors | 项目贡献者信息 |
| mailingLists | 项目的邮件列表 |
| scm | 项目源代码管理系统信息 |
| issueManagement | 项目缺陷跟踪管理系统信息 |
| **properties** | 项目自定义的属性 |
| **dependencyManagement** | 项目的依赖管理配置 |
| **dependencies** | 项目的依赖配置 |
| **repositories** | 项目的仓库配置 |
| **pluginRepositories** | 项目的插件仓库配置 |
| **build** | 项目的构建配置 |
| **profiles** | 项目的环境配置 |

文档传送门：[http://maven.apache.org/pom.html#Inheritance](http://maven.apache.org/pom.html#Inheritance)