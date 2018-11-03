+++
categories = []
date = "2018-09-24T20:52:31+08:00"
description = "maven,repository,仓库,本地仓库,远程仓库"
tags = ["maven","notepad"]
title = "Maven仓库"
draft = false
+++

> Maven仓库分为两种，一种是本地仓库，一种是远程仓库。<br>本地仓库是maven用来在本地机器上存储从远程仓库下载回来的构件的位置。<br>远程仓库包含了绝大多数流行的开源的java构件，项目依赖的构件一般都可以在这里下载。不同的远程仓库可能包含不同的java构件。

<!--more-->

#### 本地仓库和远程仓库的关系

远程仓库又分为：中央仓库（[https://repo.maven.apache.org/maven2](https://repo.maven.apache.org/maven2)）、私服（如[Nexus OSS](https://fanlychiex.github.io/post/nexus3-setup/)）、其他第三方公共库（如阿里[http://maven.aliyun.com/nexus/content/groups/public](http://maven.aliyun.com/nexus/content/groups/public)）

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/maven_repository.png)

大部分构件最初都是Maven从远程仓库下载回来存储到本地仓库的（也有一小部分是通过安装命令直接安装到本地仓库的），一旦Maven从远程仓库下载一个构件回来并存储到了本地仓库，只要你不手工删除该构件，同一个版本号的构件永远不需要去远程仓库下载第二次。因为Maven在构建项目查找依赖的构件时都首先会在本地仓库中去查找，如果在本地仓库查找不到才会去远程仓库中查找。一旦Maven在本地仓库查找到了依赖所需的构件，就不会再去远程仓库中去查找和下载了。

#### Maven本地仓库配置

在maven的用户或全局的`settings.xml`配置文件，如下：

```xml
<settings>
    
    <localRepository>D:/application/repo/maven</localRepository>
    
</settings>
```

`<localRepository>`用于指定本地仓库的位置。所有从远程仓库下载回来的构件都存储在这个位置。

#### Maven远程仓库配置

方式一，在maven的用户或全局的`settings.xml`配置文件中，找到`<profiles>`配置：

```xml
<profiles>
    <profile>
        ... ...
        <repositories>
            <repository>
                <id>nexus-maven-snapshots</id>
                <url>http://10.10.10.121:8081/repository/maven-snapshots/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </repository>
        </repositories>
        <pluginRepositories>
            <pluginRepository>
                <id>nexus-maven-snapshots</id>
                <url>http://10.10.10.121:8081/repository/maven-snapshots/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </pluginRepository>
        </pluginRepositories>
        ... ...
    </profile>
</profiles>
```

`<repositories>`和`<pluginRepositories>`在配置结构和作用上都相似，一个是用来配置远程仓库依赖类型的构件，另外一个是插件类型的构件。`<id>`必须是唯一的。`<url>`是这个仓库的地址。

方式二，在项目的`pom.xml`配置文件中，添加如下代码：

```xml
<repositories>
    <repository>
        <id>fanlychie-maven-repo</id>
        <url>https://raw.github.com/fanlychie/maven-repo/releases</url>
    </repository>
</repositories>
```

配置结构和在settings.xml中的结构一致。其区别在于，一个是在maven配置文件中配置，一个是在项目pom文件中配置。如果是针对某个或几个项目，可选择方式二来配置。

#### Maven仓库检索顺序

接下来做一个实验，在maven的用户或全局的`settings.xml`配置文件中，找到`<profiles>`配置：<br>
并且在`<repositories>`标签中配置多个仓库，并且将仓库的顺序设置为如下（用仓库ID表示）：<br>
nexus-maven-snapshots → nexus-maven-releases → nexus-maven-3rd.party

```xml
<profiles>
    <profile>
        <id>jdk7-development</id>
        <activation>
            <jdk>1.7</jdk>
            <activeByDefault>true</activeByDefault>
        </activation>
        <properties>
            <maven.compiler.source>1.7</maven.compiler.source>
            <maven.compiler.target>1.7</maven.compiler.target>
            <maven.compiler.compilerVersion>1.7</maven.compiler.compilerVersion>
        </properties>
        <repositories>
            <repository>
                <id>nexus-maven-snapshots</id>
                <url>http://10.10.10.121:8081/repository/maven-snapshots/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </repository>
            <repository>
                <id>nexus-maven-releases</id>
                <url>http://10.10.10.121:8081/repository/maven-releases/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>false</enabled>
                </snapshots>
            </repository>
            <repository>
                <id>nexus-maven-3rd.party</id>
                <url>http://10.10.10.121:8081/repository/3rd.party/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>false</enabled>
                </snapshots>
            </repository>
        </repositories>
        <pluginRepositories>
            <pluginRepository>
                <id>nexus-maven-snapshots</id>
                <url>http://10.10.10.121:8081/repository/maven-snapshots/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </pluginRepository>
            <pluginRepository>
                <id>nexus-maven-releases</id>
                <url>http://10.10.10.121:8081/repository/maven-releases/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>false</enabled>
                </snapshots>
            </pluginRepository>
            <pluginRepository>
                <id>nexus-maven-3rd.party</id>
                <url>http://10.10.10.121:8081/repository/3rd.party/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>false</enabled>
                </snapshots>
            </pluginRepository>
        </pluginRepositories>
    </profile>
</profiles>
```

然后在项目的`pom.xml`配置文件中，添加如下代码：

```xml
<repositories>
    <repository>
        <id>fanlychie-maven-repo</id>
        <url>https://raw.github.com/fanlychie/maven-repo/releases</url>
    </repository>
</repositories>

<dependencies>
    <!-- 该构件托管在fanlychie-maven-repo仓库中 -->
    <dependency>
        <groupId>org.fanlychie</groupId>
        <artifactId>jexcel</artifactId>
        <version>2.2.2</version>
    </dependency>
</dependencies>
```

然后在命令行中执行命令：

```
$ mvn compile
```

命令执行时输出的日志信息：

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/mvn_repo_sxt.png)

可以看到，maven在下载依赖所需的构件时检索仓库的顺序依次为（仓库ID）：nexus-maven-snapshots → nexus-maven-releases → nexus-maven-3rd.party → fanlychie-maven-repo → central<br>
其本质的检索顺序是：本地仓库 → maven用户或全局配置文件settings.xml中的仓库 → 项目pom.xml文件中配置的仓库 → maven[Super POM](https://fanlychiex.github.io/post/maven-super-pom)中配置的中央仓库<br>
maven在下载依赖所需的构件时，如果这个构件在本地仓库找得到，就不会再去远程仓库中查找。如果这个构件在本地仓库中查找不到，就会按远程仓库配置时的顺序，去各个仓库里面一个一个的检索，只要这个构件在其中的任意一个仓库中查找到了，就不会再继续检索其他的仓库了。

![](https://raw.githubusercontent.com/fanlychie/mdimg/master/mvn_repo_sxt2.png)

这是第二次构建项目时输出的信息，可以看到，在第一次构建成功后，再次构建项目的时候就不会再去远程仓库下载构件了。