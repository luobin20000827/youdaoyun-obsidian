---
created: 2024-05-17
updated: 2024-05-21
source: 有道云笔记迁移
---

# 第三章 XML,Tomcat & Http

## 1.XML
- XML,Extensible Markup Language 可拓展标记语言
- XML语法+HTML规范 = HTML语法
- 一般使用DOM4J进行XML解析

## 2.Tomcat10

## 2.1 基本信息
- 开源的Servlet容器，能为我们提供一个轻量化的Web服务器环境
- 启动与停止的bat脚本在bin目录下
- conf下有几个重要的配置文件：
    - server.xml，配置服务器信息
    - tomcat-users.xml：tomcat用户信息
    - web.xml：描述部署文件
    - context.xml：统一配置，一般不会修改
- webapp下是Tomcat自带的一些项目，其中Root项目对应的就是localhost:8080的默认网页
- work：运行时生成的文件（JSP生成的java和class）（每次启动时都会改变）

## 2.2 WEB项目的标准结构
![image](https://github.com/luobin7/atguigu-javaweb/raw/main/images/1681453620343.png)
- static存放静态资源
- WEB-INF及以下的几个文件的名字都不要随便修改
- 具体访问哪一个项目就是考域名中的app指定的：

![image](https://github.com/luobin7/atguigu-javaweb/raw/main/images/1681456161723.png)

## 2.3 WEB项目部署的方式
- 将项目文件或者大号的war包放在webapps项目文件下
- 在tomcat的conf下创建Catalina/localhost目录,并在该目录下准备一个app.xml文件

```
<!-- 
	path: 项目的访问路径,也是项目的上下文路径,就是在浏览器中,输入的项目名称
    docBase: 项目在磁盘中的实际路径
 -->
<Context path="/app_name" docBase="D:\mywebapps\app_name" />
```

## 2.4 使用Maven在idea集成Tomcat部署webapp项目
- 见`https://www.cnblogs.com/yif0118/p/11516303.html`
- 出现的一些问题
    - 没有添加artifact-war:exploded(热修改)
    - 没有添加指定的webapp为web包（小蓝点）
    - 大的project下面创建好几个webapps（删光src，然后新建module时选择maven-webapp骨架）
    - java运行版本不对，这个可以去pom文件里加，或者在setting里面的javacompiler里面改