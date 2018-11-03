+++
categories = []
date = "2018-09-24T20:32:31+08:00"
description = "maven,lifecycle,生命周期"
tags = ["maven","notepad"]
title = "Maven生命周期"
draft = false
+++

> [Maven的生命周期](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)是对项目所有的构建过程进行抽象和统一。它包含了项目的清理、初始化、编译、测试、打包、部署和站点生成等构建步骤。Maven的生命周期本质是定义项目构建的各个步骤，它本身是抽象的，并不作任何的具体工作，而是将构建过程中的各个步骤任务交个相对应的插件来完成。这使得Maven的生命周期具有良好的扩展性，开发者可以自己编写插件实现代码绑定到构建的某个步骤。当然，在绝大部分场景下，开发者不必这样来做，因为Maven初始为项目构建的各个步骤绑定了默认的插件。

<!--more-->

#### 生命周期

Maven有三个相互独立的生命周期。它们分别是：`Clean` `Default` `Site`。

| 生命周期 | 描述 |
| ---- | ---- |
| Clean | 用于清理项目 |
| Default | 用于构建项目 |
| Site | 用于生成项目站点 |

每个独立的生命周期都包含一些阶段（phase），这些阶段是一个有序的组合，后面的阶段依赖于前面的阶段。Maven主要的生命周期阶段在下表中已用加粗字体标出。

##### Clean生命周期

| 阶段 | 描述 |
| ---- | ---- |
| pre-clean | 在实际项目清理之前执行所需的过程 |
| **clean** | 删除上一个版本生成的所有文件 |
| post-clean | 执行完成项目清理所需的过程 |

##### Default生命周期

| 阶段 | 描述 |
| ---- | ---- |
| validate | 验证项目所有必要的信息都是可用的 |
| initialize | 初始化构建状态，例如设置属性或创建目录 |
| generate-sources | 生成包含在编译中的源代码 |
| process-sources | 处理源代码，过滤和替换一些变量的值 |
| generate-resources | 生成包含在包中的资源 |
| process-resources | 处理资源包，将其复制到目标目录 |
| **compile** | 编译项目的源代码 |
| process-classes | 从编译中对生成的文件进行后处理，如对Java类进行字节码增强 |
| generate-test-sources | 生成包含在编译中的测试源代码 |
| process-test-sources | 处理源代码，过滤和替换一些变量的值 |
| generate-test-resources | 生成包含在测试包中的资源 |
| process-test-resources | 处理资源包，将其复制到测试目标目录 |
| test-compile | 编译项目的测试源代码 |
| process-test-classes | 从编译中对生成的测试文件进行后处理，如对Java类进行字节码增强 |
| **test** | 执行单元测试，测试代码不会被打包部署 |
| prepare-package | 打包之前执行所需的任何操作 |
| **package** | 打包项目 |
| pre-integration-test | 集成测试前执行的操作，如设置环境等 |
| integration-test | 执行集成测试，如果需要，可将软件包部署到可运行集成测试的环境 |
| post-integration-test | 集成测试后执行的操作，如清理环境 |
| verify | 检查软件包是否符合标准 |
| **install** | 将项目输出构件安装到本地仓库 |
| **deploy** | 将项目输出构件部署到远程仓库 |

##### Site生命周期

| 阶段 | 描述 |
| ---- | ---- |
| pre-site | 在实际项目站点生成之前执行所需的过程 |
| **site** | 生成项目的站点文档 |
| post-site | 执行完成站点生成所需的进程，并准备站点部署 |
| site-deploy | 将生成的站点文档部署到指定的Web服务器 |

##### 命令行命令

Maven执行项目构建的过程实质上是调用Maven生命周期阶段的过程。Maven主要的生命周期阶段并不多，其命令行命令几乎都是基于这些阶段的简单组合。而且Maven的大三生命周期是相互独立的，每个生命周期的阶段都有前后依赖的关系。任何一个简单的或组合的命令，只有当前面的阶段执行成功，后面的阶段才有机会得以继续执行。如果前面的某个阶段执行失败，后面的阶段就会被短路而导致无法继续执行下去。

```
$ mvn clean deploy
```

- `clean`<span style="padding-left:7px"></span>：调用Clean生命周期的clean和clean之前的所有阶段（即pre-clean和clean）
- `deploy`：调用Default生命周期compile + test + package + install + deploy五个主要的阶段

因此，执行该命令最终达到的效果是：<br>清理项目 → 编译项目 → 单元测试 → 打包项目 → 将软件包安装到本地 → 将软件包发布到远程仓库

#### 生命周期内置绑定的插件

Maven的生命周期是抽象的，它本身不做任何具体的工作，项目构建的过程实质是调用Maven生命周期阶段的过程，而Maven初始已经为生命周期的大部分阶段绑定了默认的插件，当外部调用Maven生命周期阶段的时候，绑定到这些阶段的插件就会来执行对应的具体工作。

Clean生命周期内置绑定的插件（详见[Lifecycles Reference](http://maven.apache.org/ref/3.5.4/maven-core/lifecycles.html)）

| 阶段 | 插件 |
| ---- | ---- |
| pre-clean |  |
| **clean** | maven-clean-plugin:2.5:clean |
| post-clean |  |

Default生命周期内置绑定的插件（详见[Plugin Bindings for default Lifecycle Reference](http://maven.apache.org/ref/3.5.4/maven-core/default-bindings.html)）

| 阶段 | 插件 |
| ---- | ---- |
| process-sources | maven-resources-plugin:2.6:resources |
| **compile** | maven-compiler-plugin:3.1:compile |
| process-test-sources | maven-resources-plugin:2.6:testResources |
| test-compile | maven-compiler-plugin:3.1:testCompile |
| **test** | maven-surefire-plugin:2.12.4:test |
| **package** | maven-jar-plugin:2.4:jar<br>maven-ejb-plugin:2.3:ejb<br>maven-plugin-plugin:3.2:addPluginArtifactMetadata<br>maven-war-plugin:2.2:war<br>maven-ear-plugin:2.8:ear<br>maven-rar-plugin:2.2:rar |
| **install** | maven-install-plugin:2.4:install |
| **deploy** | maven-deploy-plugin:2.7:deploy |

Site生命周期内置绑定的插件（详见[Lifecycles Reference](http://maven.apache.org/ref/3.5.4/maven-core/lifecycles.html)）

| 阶段 | 插件 |
| ---- | ---- |
| pre-site |  |
| **site** | maven-site-plugin:3.3:site |
| post-site |  |
| site-deploy | maven-site-plugin:3.3:deploy |

#### 项目打包类型与生命周期内置绑定的插件

Maven项目的打包类型是由`<packaging>xxoo</packaging>`标签元素的值来决定的。可选值常见的有`jar` `war` `pom`，以及不太常用的`maven-plugin` `ejb` `ear` `rar`等。<br>
Maven的Default生命周期为不同的打包类型内置绑定了不同的插件。而我们知道，项目构建过程的具体工作是由插件来完成的，因此不同的打包类型调用相同的Maven生命周期阶段，最终得以打出不同类型的软件包。

`<packaging>pom</packaging>`类型打包方式与生命周期内置绑定的插件

```xml
<phases>
    <install>
        org.apache.maven.plugins:maven-install-plugin:2.4:install
    </install>
    <deploy>
        org.apache.maven.plugins:maven-deploy-plugin:2.7:deploy
    </deploy>
</phases>
```

`<packaging>jar</packaging>`类型打包方式与生命周期内置绑定的插件

```xml
<phases>
    <process-resources>
        org.apache.maven.plugins:maven-resources-plugin:2.6:resources
    </process-resources>
    <compile>
        org.apache.maven.plugins:maven-compiler-plugin:3.1:compile
    </compile>
    <process-test-resources>
        org.apache.maven.plugins:maven-resources-plugin:2.6:testResources
    </process-test-resources>
    <test-compile>
        org.apache.maven.plugins:maven-compiler-plugin:3.1:testCompile
    </test-compile>
    <test>
        org.apache.maven.plugins:maven-surefire-plugin:2.12.4:test
    </test>
    <package>
        org.apache.maven.plugins:maven-jar-plugin:2.4:jar
    </package>
    <install>
        org.apache.maven.plugins:maven-install-plugin:2.4:install
    </install>
    <deploy>
        org.apache.maven.plugins:maven-deploy-plugin:2.7:deploy
    </deploy>
</phases>
```

`<packaging>war</packaging>`类型打包方式与生命周期内置绑定的插件

```xml
<phases>
    <process-resources>
        org.apache.maven.plugins:maven-resources-plugin:2.6:resources
    </process-resources>
    <compile>
        org.apache.maven.plugins:maven-compiler-plugin:3.1:compile
    </compile>
    <process-test-resources>
        org.apache.maven.plugins:maven-resources-plugin:2.6:testResources
    </process-test-resources>
    <test-compile>
        org.apache.maven.plugins:maven-compiler-plugin:3.1:testCompile
    </test-compile>
    <test>
        org.apache.maven.plugins:maven-surefire-plugin:2.12.4:test
    </test>
    <package>
        org.apache.maven.plugins:maven-war-plugin:2.2:war
    </package>
    <install>
        org.apache.maven.plugins:maven-install-plugin:2.4:install
    </install>
    <deploy>
        org.apache.maven.plugins:maven-deploy-plugin:2.7:deploy
    </deploy>
</phases>
```