---
created: 2024-07-17
updated: 2024-07-27
source: 有道云笔记迁移
tags:
  - 学习笔记
  - SpringBoot
---

## 1.SpringBoot的特点
### 1.1 依赖管理
- 父项目为spring-boot-starter-parent，之后一些常用的jar包依赖springboot会自动为我们做依赖管理，我们只需要引入坐标无需声明版本

    - 在某些情况下需要使用特定的版本则单独声明即可
    - spring-boot-starter-xxx 则是springboot针对不同场景进一步整理的依赖工具集
    - 如果是自定义的一般命名为xxx-spring-boot-starter
```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.4.3</version>
    <relativePath/>
</parent>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency> 无需声明版本，springboot自动管理
```

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```


### 1.2 自动配置
- @SpringBootApplication实现自动装配
- 主程序下（@SpringBootApplication启动类所在的包）所有的包都会扫描为bean（约定）
- 查看源码，@SpringBootApplication(复合注解)中的核心注解就是
@SpringBootConfiguration @EnableAutoConfiguration
@ComponentScan


## 2.容器功能
### 2.1 组件添加
1. @Configuration

- 标记这是一个标记类，结合@Bean将标记类下的每一个方法都注册为一个方法名的bean
- 和Spring的xml-<bean>是一样的
- 默认热加载和单例
- @Bean和@Component、@Controller、@Service、@Repository的区别（方法/类，精细/粗放）
- @Import类似lombok的简化注解，能快速实现一个对应.class的bean
- @ImportResource("classpath:beans.xml")导入xml中的配置
- @Conditional注解，触发条件后才实现bean的注册
    - 提供一些现成的条件判断，也可以通过重写Condition接口自定义

### 2.2 配置绑定
- @ConfigurationProperties(prefix=)
    - 作用和@Value类似，能让类纳入组件管理时自动去配置文件中寻找配置值
    - 这个方法可以配置在类上也可以配置在方法上
    - 底层server.port都是通过它实现的

### 2.3 SpringBoot自动装配流程
- 从@SpringBootApplication注解出发：核心就是@EnableAutoConfiguration
- 导入了AutoConfigurationImportSelector委托SpringFactoriesLoader去读取启动类jar包中的META-INF/spring.factories文件, 并加载里面配置的自动配置对象，

## 3.yaml配置文件
- kv写法，kv之间有空格
    - port: 8088
- 缩进使用空格，表示层级关系

```
person:
    name: Lucy
    和
properties中person.name的效果是一样的
```
- 字符串不需要加引号
- 数组活列表的写法

```
行内写法：  k: [v1,v2,v3]
#或者
k:
 - v1
 - v2
 - v3
```

## 相关笔记
所属索引：[[SpringBoot-MOC]]
- [[Spring事务]] — 事务专题
- [[Spirng]] — Spring 基础
