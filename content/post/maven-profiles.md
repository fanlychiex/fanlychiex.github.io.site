+++
categories = []
date = "2018-09-25T23:22:31+08:00"
description = "maven,multiple,multi,modules,profile,profiles,多环境,部署,打包,配置"
tags = ["maven","notepad"]
title = "Maven多环境构建配置"
draft = false
+++

> 一个项目从开发到最后发布上线，通常需要在多套不同的环境经受反复的测试和验证，例如开发环境、测试环境、预生产环境、生产环境等。项目部署到不同的环境时，项目的配置通常也是不同的，例如数据库的数据源配置等。maven提供了一套`profiles`配置，开发者可以在项目POM文件中预先定义好若干个不同环境的`profile`配置，项目可以根据不同的构建参数来动态选择其中的一个环境设置。这就意味着相同的一套项目代码，可以在构建时根据传入的不同环境参数打出不同环境的软件包来。这也是maven竭力保证的软件可移植性。

<!--more-->

#### 多环境配置

项目`pom.xml`配置示例如下：

```xml
<!-- 多环境构建配置 -->
<profiles>
    <!-- 开发环境配置 -->
    <profile>
        <!-- 标识符, 可以通过 -P[id] 来激活 --> <!-- -Pdev -->
        <id>dev</id>
        <!-- 激活条件 -->
        <activation>
            <!-- 可以通过 -D[name]=[value] 来激活 --> <!-- -Denv=dev -->
            <property>
                <name>env</name>
                <value>dev</value>
            </property>
            <!-- 默认激活 -->
            <activeByDefault>true</activeByDefault>
        </activation>
        <!-- 自定义的属性, 可以通过 ${label} 来引用 --> <!-- ${jdbcUrl} -->
        <properties>
            <jdbcUrl>jdbc:mysql://localhost:3306/dev</jdbcUrl>
        </properties>
    </profile>
    <!-- 测试环境配置 -->
    <profile>
        <!-- 标识符, 可以通过 -P[id] 来激活 --> <!-- -Ptest -->
        <id>test</id>
        <!-- 激活条件 -->
        <activation>
            <!-- 可以通过 -D[name]=[value] 来激活 --> <!-- -Denv=dev -->
            <property>
                <name>env</name>
                <value>test</value>
            </property>
        </activation>
        <!-- 自定义的属性, 可以通过 ${label} 来引用 --> <!-- ${jdbcUrl} -->
        <properties>
            <jdbcUrl>jdbc:mysql://localhost:3306/test</jdbcUrl>
        </properties>
    </profile>
</profiles>
```

自定义属性`<properties>`需要配合`<resources>`使用才能发挥作用：

```xml
<build>
    <!-- 类路径资源配置, 最终输出到软件包中 -->
    <resources>
        <resource>
            <!-- 资源目录路径, 此路径是相对当前POM文件的位置 -->
            <directory>src/main/resources</directory>
            <!-- filtering=true, 能够代入具体的值替换${label}占位符 -->
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```

项目配置文件`src/main/resources/environment.properties`的内容如下：

```
mysql.jdbc.url = ${jdbcUrl}
```

打包开发环境的软件包：

```
$ mvn clean -Pdev package
```

或：

```
$ mvn clean -Denv=dev package
```

打包测试环境的软件包：

```
$ mvn clean -Ptest package
```

或：

```
$ mvn clean -Denv=test package
```

#### 配置的方式

maven支持的`profile`配置方式主要有以下几种：

1. 与项目相关的`profile`配置可以定义在项目`pom.xml`文件中；
2. 与用户相关的`profile`配置可以定义在用户`settings.xml`（`%USER_HOME%/.m2/settings.xml`）文件中；
3. 全局的`profile`配置可以定义在全局`settings.xml`（`%MAVEN_HOME%/.m2/settings.xml`）文件中；

外部文件（用户或全局`settings.xml`文件）方式配置的`profile`是无法移植的。这种方式你只能通过配置`<repositories>`、`<pluginRepositories>`、`<properties>`三个标签元素来影响构建的过程，但并不能改变构建最终输出的结果。

内嵌配置（项目`pom.xml`文件）方式配置的`profile`是在项目内部定义的，因此它具有良好的可移植性。这种方式你有更多的标签元素可选择，并且能够改变构建最终输出的结果。这些标签元素有：

```xml
<profile>
    <!-- 参与构建的模块 -->
    <modules>...</modules>
    <!-- 构件依赖声明 -->
    <dependencies>...</dependencies>
    <!-- 构件依赖管理 -->
    <dependencyManagement>...</dependencyManagement>
    <!-- 构件发布管理 -->
    <distributionManagement>...</distributionManagement>
    <!-- 插件仓库 -->
    <pluginRepositories>...</pluginRepositories>
    <!-- 自定义属性 -->
    <properties>...</properties>
    <!-- 生成站点的报告信息 -->
    <reporting>...</reporting>
    <!-- 构件仓库 -->
    <repositories>...</repositories>
    <!-- 构建配置 -->
    <build>
        <!-- 默认的构建目标, 在命令行中如果直接执行mvn没有带生命周期阶段, 则默认执行此处配置的阶段 -->
        <defaultGoal>...</defaultGoal>
        <!-- 类路径资源 -->
        <resources>...</resources>
        <!-- 单元测试的类路径资源 -->
        <testResources>...</testResources>
        <!-- 打包的名称 -->
        <finalName>...</finalName>
    </build>
</profile>
```

#### 激活方式

用户或全局`settings.xml`以及项目`pom.xml`的配置可以使用`<activeByDefault>`来激活：

```xml
<profile>
    <id>dev</id>
    <activation>
        <!-- 默认激活 -->
        <activeByDefault>true</activeByDefault>
    </activation>
</profile>
```

用户或全局`settings.xml`的配置还可以使用`<activeProfiles>`来激活：

```xml
<profile>
    <id>dev</id>
</profile>
<activeProfiles>
    <!-- 根据ID标识符来激活 -->
    <activeProfile>dev</activeProfile>
</activeProfiles>
```

命令行可以使用`-P[id]`或`-P [id]`或`-P [id1,id2]`来激活：

```xml
<profile>
    <id>dev</id>
</profile>
<profile>
    <id>test</id>
</profile>
```

```
$ mvn clean -Pdev package
$ mvn clean -P dev package
$ mvn clean -P dev,test package
```

命令行还可以使用`-D[name]=[value]`的方式来激活：

```xml
<profile>
    <activation>
        <property>
            <name>env</name>
            <value>dev</value>
        </property>
    </activation>
</profile>
```

```
$ mvn clean -Denv=dev package
```

#### 查看激活

```
$ mvn help:active-profiles
```

输出信息示例如下：

```html
The following profiles are active:

 - jdk7-development (source: external)
 - dev (source: org.fanlychie:maven-multiple-modules-sample:0.0.2-SNAPSHOT)
```

文档传送门：[http://maven.apache.org/guides/introduction/introduction-to-profiles.html](http://maven.apache.org/guides/introduction/introduction-to-profiles.html)

文档传送门：[http://maven.apache.org/pom.html#Profiles](http://maven.apache.org/pom.html#Profiles)

#### 示例项目源码

[https://github.com/fanlychie/maven-multiple-modules-samples/tree/master/hierachical-project-layout/maven-multiple-modules-sample](https://github.com/fanlychie/maven-multiple-modules-samples/tree/master/hierachical-project-layout/maven-multiple-modules-sample)