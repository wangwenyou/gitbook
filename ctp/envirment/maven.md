# 为什么使用maven

* Maven最大化的消除了构建的重复
* Maven可以帮助我们标准化构建过程。所有的项目都是简单一致的，简化了学习成本
* Maven不仅是构建工具，他还是一个项目管理工具:他还提供了中央仓库，统一依赖的来源，避免了依赖冗余与不一致
* Maven对于项目目录结构、测试用例命名方式等内容都有既定的规则，只要遵循了这些成熟的规则，用户在项目间切换的时候就免去了额外的学习成本，可以说是约定优于配置

# 工程组织

```
因产品编译要求，统一使用maven3.3.3，Java1.8
```

* 使用标准的工程结构（详见解耦工程结构）
* 工程名称使用统一的工程命名（详见结构工程命名）
* 业务代码统一放在包com.seeyon下,如news组的代码放在
  ```
  apps-news
      └──src
          ├──main
          |    ├──java
          |    |    └──com
          |    |        └──seeyon
          |    |              └──news
          |    |                   └──NewsManager.java
          |    ├──resources
          |    └──webapp
          └──test
               ├──java
               |    └──com
               |        └──seeyon
               |              └──news
               |                   └──NewsManagerTest.java
               └──resources

  ```
* 构建jar包名统一使用标准的命名结构（详见结构版本管理）

# POM（项目对象模型）

**Project Object Model**，项目对象模型。通过xml格式保存的pom.xml文件。作用类似ant的build.xml文件，功能更强大。该文件用于管理：源代码、配置文件、开发者的信息和角色、问题追踪系统、组织信息、项目授权、项目的url、项目的依赖关系等等。

```
pom.xml文件由平台统一管理，所有对依赖、配置的更改都需要向平台申请。禁止项目组对工程的自定义。
```

## 项目规范

* maven配置文件统一使用工程根目录下的settings.xml，配置会使用统一的内部nexus服务器
* 各业务组工程独立，仅允许依赖平台，不允许业务组之间相互依赖

  * _平台jar_
    ```
    <
    dependency
    >
    <
    groupId
    >
    com.seeyon
    <
    /groupId
    >
    <
    artifactId
    >
    ctp-core
    <
    /artifactId
    >
    <
    version
    >
    ${ctp.version}
    <
    /version
    >
    <
    /dependency
    >
    ```
  * _单元测试基础jar_
    ```
    <
    dependency
    >
    <
    groupId
    >
    com.seeyon
    <
    /groupId
    >
    <
    artifactId
    >
    ctp-test
    <
    /artifactId
    >
    <
    version
    >
    ${ctp.version}
    <
    /version
    >
    <
    /dependency
    >
    ```

* 不允许依赖本地jar包，所有jar包依赖都来自于pom文件中的定义
* 平台会提供标准的pom文件，平台会协助各业务组开发负责人进行维护，pom文件的修改需要平台认可
* 依赖更新、工程配置、工程构建仅允许通过maven来实现

# CI（持续集成）

## 构建主流程

![](http://www.websequencediagrams.com/?img=msc9gGVjz)

## 构建任务属性

* 统一使用UTF-8编码
* 测试类名统一使用Test结尾
* 测试类的数据准备类名统一使用DataInit结尾
* 测试报告中不会统计测试类以及数据准备类的信息\(请注意类名定义以免无法正常测试\)
* 单个构建任务会清空历史信息，流程：

  > [![](http://open.seeyon.com:4567/tmp/55f451aece9e0be836c79cf3b9857fc6eacee4f9.png "Graphviz image")](http://open.seeyon.com:4567/tmp/55f451aece9e0be836c79cf3b9857fc6eacee4f9.png)

* 任何任务失败都不会执行后续任务

## 插件

### Jacoco

```
IDE以及持续集成中都使用Jacoco进行单元测试以及覆盖率分析
```

![](http://open.seeyon.com:4567/Maven/QQ20150723-1@2x.jpg)

![](http://open.seeyon.com:4567/Maven/QQ20150723-2@2x.jpg)

### SonarQube

```
项目静态代码分析使用SonarQube管理
```

![](http://open.seeyon.com:4567/Maven/QQ20150723-3@2x.jpg)

![](http://open.seeyon.com:4567/Maven/QQ20150723-4@2x.jpg)

