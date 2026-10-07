---
created: 2024-02-01
updated: 2024-02-02
source: 有道云笔记迁移
---

# 第一章：Java概述

# 1.JDK和JRE

JDK:Java Development kit ：Java开发工具包

JRE:Java Runtime Enviroment ：Java程序的运行时环境，包含JVM
和运行时所需要的核心类库

![image](https://github.com/luobin7/-java-/raw/main/%E7%AC%AC01%E7%AB%A0_Java%E8%AF%AD%E8%A8%80%E6%A6%82%E8%BF%B0/images/image-20220310200731185.png)

# 2.Java运行
java兼具**编译型语言**和**解释型语言**的特点，他先通过javac编译为字节码文件，后在jvm虚拟机上执行

![image](https://github.com/luobin7/-java-/raw/main/%E7%AC%AC01%E7%AB%A0_Java%E8%AF%AD%E8%A8%80%E6%A6%82%E8%BF%B0/images/image-20220310230210728.png)

# 3.运行HelloWorld

```
public class HelloWorld {
  public static void main(String[] args) {
      System.out.println("Hello World!");
  }
}
```
## 3.1 添加环境变量

* 系统变量中添加Java Home：'your path to jdk'

* 用户变量Path中添加%JAVA_HOME%\bin

## 3.2 多个字节码文件
* 每一个类都会生成一个字节码文件，且字节码文件的名称就是类名

## 3.3 java程序的结构与格式
```
类{
    方法{
        语句;
    }
}
```
* 每一级缩进一个tab制表位
* Java程序的入口是main方法
```
public static void main(String[] args){
    
}
```

## 3.4 源文件与类名
* Java要求一个java文件中只能声明一个public公共类，且文件名与public类的名称必须完全匹配，包括大小写
* 每一个类都会生成一个字节码文件，且字节码文件的名称就是类名

## 4.注释

单行注释
> // 注释内容

多行注释
 > /*
 注释段
 */

文档注释
> /**
  @author  指定java程序的作者
  @version  指定源文件的版本
*/

文档注释可以被JDK提供的javadoc工具解析，自动生成说明文档
```
javadoc -encoding utf-8 -d your_doc_name -author -version HelloWorld.java
```
**注释是不可以嵌套的**


## 5.Java API 文档

> https://docs.oracle.com/en/java/javase/17/docs/api/index.html

## 6.JVM
* 跨平台( **Write once , Run Anywhere** )
* 自动内存管理