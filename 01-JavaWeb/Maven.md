---
created: 2024-05-09
updated: 2024-09-18
source: 有道云笔记迁移
---

# 第一章 Maven概述
## 1.1 为什么需要Maven
- jar包依赖很复杂，需要有专门的管理工具
- 脱离ide环境项目架构仍然需要构建
- CI/CD整合

## 1.2 什么是Maven
- pache基金会维护的一款为Java项目提供构建与依赖支持的工具
- 本质上是一个项目管理工具，提供标准化的构建方式，快捷的jar包依赖管理和一套统一的项目文件结构


## 1.3 Maven配置
- 配置MAVEN_HOME
- 配置Path
- (设置本地repository)
- IDEA配置Maven（替换自带的）

# 第二章：基于IDEA创建Maven工程

## 2.1 GAVP坐标概念
- 定位Maven仓库中的任意一个jar包只需要四个标识来定位：
    - groupId：公司/组织名：com/org.公司名.业务线.子业务线,z最多四级
    - artifactId：项目/模块id：产品线-模块名
    - version：版本：主版本-次版本-修订号
    - Packaging规则：项目类型(默认为jar)

```
// 打开Maven项目中的pro.xml中
    <groupId>org.example</groupId>
    <artifactId>JavaWeb_MAVEN</artifactId>
    <version>1.0-SNAPSHOT</version>
```

## 2.2 依赖添加

- 将新的依赖添加到pom.xml后refresh
- 依赖可以查询mvn-repository

## 2.3 Maven工程项目结构

```
|-- pom.xml                               # Maven 项目管理文件 
|-- src
    |-- main                              # 项目主要代码
    |   |-- java                          # Java 源代码目录
    |   |   `-- com/example/myapp         # 开发者代码主目录
    |   |       |-- controller            # 存放 Controller 层代码的目录
    |   |       |-- service               # 存放 Service 层代码的目录
    |   |       |-- dao                   # 存放 DAO 层代码的目录
    |   |       `-- model                 # 存放数据模型的目录
    |   |-- resources                     # 资源目录，存放配置文件、静态资源等
    |   |   |-- log4j.properties          # 日志配置文件
    |   |   |-- spring-mybatis.xml        # Spring Mybatis 配置文件
    |   |   `-- static                    # 存放静态资源的目录
    |   |       |-- css                   # 存放 CSS 文件的目录
    |   |       |-- js                    # 存放 JavaScript 文件的目录
    |   |       `-- images                # 存放图片资源的目录
    |   `-- webapp                        # 存放 WEB 相关配置和资源
    |       |-- WEB-INF                   # 存放 WEB 应用配置文件
    |       |   |-- web.xml               # Web 应用的部署描述文件
    |       |   `-- classes               # 存放编译后的 class 文件
    |       `-- index.html                # Web 应用入口页面
    `-- test                              # 项目测试代码
        |-- java                          # 单元测试目录
        `-- resources                     # 测试资源目录
```
## 3.Maven项目的构建
- 构建一般用到四个命令:compile,package,install,deploy
- compile只是将.java编译为.class字节码文件
- package做了clean、resources、compile、testResources、testCompile、test、jar共7个阶段
- install在package基础上将当前打包好的jar包部署到本地的maven仓库中
    - install时遇到一个本地仓库有对应依赖的jar包，但是install就是无法识别，反复提示去远程仓库中查找对应jar包的bug
        - 网上有一个帖子让删除本地仓库中例如remoterepo.properties或是.lastUpdated文件这些标识待验证/错误的缓存文件，例如 [这篇文章](https://www.cnblogs.com/dasusu/p/11825786.html)试了一下没有用
        - 最后时通过一个强制install本地jar到依赖中的命令实现的`mvn install:install-file -Dfile=<path-to-jar-file> -DgroupId=<group-id> -DartifactId=<artifact-id> -Dversion=<version> -Dpackaging=jar`
            - 这中间还遇到一个坑：在idea新建一个默认的maven terminal默认使用的是powershell，无法正确识别这个命令中的参数部分，要改成使用cmd，[查看这里](https://blog.csdn.net/wangpaiblog/article/details/121020069)
- deploy在install基础上还将jar包部署到远端的nexus-maven仓库中
- maven鉴于他的一个反应堆机制，如果你引用了一个本地模块，他优先是去项目代码中依赖，其次是本地仓库中，再次是远程仓库中。
    