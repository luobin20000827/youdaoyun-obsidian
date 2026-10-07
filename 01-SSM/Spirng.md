---
created: 2024-06-06
updated: 2024-07-09
source: 有道云笔记迁移
---

# 1.Spring简介
 - 中文文档：https://www.docs4dev.com/docs/zh/spring-framework/5.1.3.RELEASE/reference

## 1.1 mvn依赖
- 引入spring-jdbc和spring-mvc就可以了

## 1.2 优点
- 轻量级的、非入侵式的框架（容器）
- IOC（控制反转）与AOP（切面式编程）
- 支持事务

## 1.3 七大模块
![image](https://images2017.cnblogs.com/blog/1219227/201709/1219227-20170930225010356-45057485.gif)
- Spring - SpringBoot - SpringCloud

# 2. Spring IOC
- 引例：传统web结构下dao层与service层间的耦合
    - 需要调用dao层的新实现时，我们需要在service层new新的dao实体，改动时牵一发而动全身
- 为此Spring引入了控制反转IOC(Inversion Of Control)思想
    - 控制反转：指将获取依赖对象的形式反转：Spring中指从程序流反转给外部容器
    - 而依赖注入DI(Dependency Injection)则是Spring实现IOC思想的一种具体方式，将对象实例的创建和依赖关系的维护都交给了 Spring 容器，我们只需要通过配置或注解的方式告诉 Spring 依赖关系和创建对象的规则。（将new操作变为实例的注入）

## 2.1 HelloSpring

```
public class Hello {
    private String name;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public void show(){
        System.out.println("Hello,"+ name );
    }
}
```

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--bean就是java对象 , 由Spring创建和管理-->
    <bean id="hello" class="com.kuang.pojo.Hello">
        <property name="name" value="Spring"/>
    </bean>
</beans>
```

```
@Test
public void test(){
    //解析beans.xml文件 , 生成管理相应的Bean对象
    ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
    //getBean : 参数即为spring配置文件中bean的id .
    Hello hello = (Hello) context.getBean("hello");
    hello.show();
}
```
## 2.2 Spring配置
- xml解析器-context对象-getbean方法
- 这里的bean的创建使用的是无参构造的方法
    - <property>属性中的name标签表示通过反射实体类中的set方法给他附上value中的值
    - ref表示给setname方法传的是引用对象，value则表示基本数据类型
- bean同样可以通过有参构造方法来构建

```
<!-- 第一种根据index参数下标设置 -->
<bean id="userT" class="com.kuang.pojo.UserT">
    <!-- index指构造方法 , 下标从0开始 -->
    <constructor-arg index="0" value="kuangshen2"/>
</bean>
```
```
<!-- 第二种根据参数名字设置 -->
<bean id="userT" class="com.kuang.pojo.UserT">
    <!-- name指参数名 -->
    <constructor-arg name="name" value="kuangshen2"/>
</bean>
```

```
<!-- 第三种根据参数类型设置 -->
<bean id="userT" class="com.kuang.pojo.UserT">
    <constructor-arg type="java.lang.String" value="kuangshen2"/>
</bean>
```
- 可以使用import标签将多个bean文件合并到一个bean文件中一起导入

## 2.3 依赖注入DI
- 依赖：指bean对象的创建依赖于外部容器
- 注入指bean对象依赖的资源由容器来配置和装配（怎么从xml中配置出一个创建出一个满足属性的bean实体）
- 在配置文件加载时，容器中的bean就已经加载完成了

### 注入方式

- 常量注入(property value)
- bean注入(将bean注入到另一个bean)(property ref)
- 数组注入(property-array-value)
- List注入(property-list-value)
- map注入(property-map-entry(key-value))
- set注入(property-set-value)
- null注入，property-null，这个和不写这一项的效果是一样的
- properties注入(property-props-prop(key)value)
- 此处的list和map和array都是最基础的数据类型，如果要注入util扩展的数据类型还要额外在头文件中引入

```
http://www.springframework.org/schema/util
http://www.springframework.org/schema/util/spring-util-4.0.xsd
xmlns:util="http://www.springframework.org/schema/util
```







### 拓展注入方式
- p命名空间注入:properties

```
导入约束 : xmlns:p="http://www.springframework.org/schema/p"
<!--P(属性: properties)命名空间 , 属性依然要设置set方法-->
<bean id="user" class="com.kuang.pojo.User" p:name="狂神" p:age="18"/>
```

- c命名空间注入:constructor

```
导入约束 : xmlns:c="http://www.springframework.org/schema/c"
<!--C(构造: Constructor)命名空间 , 属性依然要设置set方法-->
<bean id="user" class="com.kuang.pojo.User" c:name="狂神" c:age="18"/>
```

### 方法注入
- 解决单例 -（依赖）- 多例这种依赖关系提出的一种机制
- `Lookup`关键字

## 2.4 Bean作用域
- 由定义时的scope属性定义
- Singleton单例模式，默认选择
![image](https://docs.spring.io/spring-framework/reference/_images/singleton.png)
- Prototype原型模式
![image](https://docs.spring.io/spring-framework/reference/_images/prototype.png)
- 还有request、application和session模式，与web端中的概念相似


## 2.4 Bean的自动装配
- Spring用于解决Bean依赖的一种机制，会在应用上下文中为某个bean寻找其依赖的bean。
- Spring中bean有三种装配机制，分别是：
    - 在xml中显式配置
    - 在java中显式配置
    - 隐式的bean发现机制和自动装备
- 自动装配：atuowired="byName"/"byType"（声明在需要装配属性u的类中）
    - byName:Name全局唯一
    - byType：类型全局唯一

### 基于注解的自动装配
- 开启注解：`<context:annotation-config/>`
- @AtuoWired
    - 注在方法/参数/构造器上，让所需要的值从ioc容器中自动获取
    - 默认采用的是byType的匹配方式
    - 要开启byName，可以再加一个`@Qualifier`
    - 加入(required = false),这样框架找不到对应的bean也不会报错
- @Resource
    - 先按Name匹配，再按Type匹配

## 2.5 注解开发
- 使用注解开发需要引入aop包，同时在配置文件中引入context约束
- 统一配置文件一般起名为applicationContext.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
</beans>
```

### Bean的实现注解：@Component("name")
- 注在需要装配为bean的组件上
- 相当于<bean id="name" class="namespace">
- 后续的赋值就可以使用set方法或是@Value注解
    - @Value("value")，可以加载属性或是set方法上 :作用类似于给bean的属性赋一个初始值
    - @Scope("singleton"/"prototype") 通过注解给bean定义作用域
- Component组件可以进一步细分为
    - ##### @Controller：web层
    - ##### @Service：service层
    - ##### @Repository：dao层
    
### 自动装配注解：@AutoWired
- 见前文


### JavaConfig配置Spring：@Configuration和@Bean
- 在类前加@Configuration，，表示这个类是Spring的配置类，
- 在方法前加@Bean，装配一个方法返回值类型的Bean，name就是方法名
- 也可以不加@Configuration，这样方法返回的Bean就不能保证是单例的

# 3. 代理机制
- 一种为了在不改变原有类（被代理类）的基础上，通过引入代理类来对被代理类进行增强或者控制的设计模式，
    - 通过在代理类中引入被代理类的对象应用实现
    - 增强：在实现被代理类方法的同时，代理类可以新增一些方法：日志、事务管理
    - 控制：隔离Client对象对被代理对象直接访问：做访问/安全性控制
- 开闭设计原则：扩展开放，修改封闭
![image](https://img-blog.csdnimg.cn/img_convert/cf79ba1aa39f4dcd088b68f00ef2fb86.gif)
- Java中有三种代理方式
    - 静态代理
    - JDK动态代理
    - CGLib动态代理
    
## 3.1 静态代理
![image](https://img-blog.csdnimg.cn/direct/94398e9e905a4e668c10bb63aa7789fc.png)
- 创建一个代理类，通过构造器或set方法实现与被代理类之间的依赖，重写调用原对象方法实现方法的增强
- 例如下文通过代理简单实现日志功能

```
//代理角色，在这里面增加日志的实现
public class UserServiceProxy implements UserService {
    private UserServiceImpl userService;
    public void setUserService(UserServiceImpl userService) {
        this.userService = userService;
    }
    public void add() {
        log("add");
        userService.add();
    }
    public void delete() {
        log("delete");
        userService.delete();
    }
    public void update() {
        log("update");
        userService.update();
    }
    public void query() {
        log("query");
        userService.query();
    }
    public void log(String msg){
        System.out.println("执行了"+msg+"方法");
    }
}
```
- 静态代理的缺陷也很明显
    - 需要手动创建代理类，且每一个被代理对象都要新增一个代理类
    - 代理类依赖被代理类，存在一定的耦合

## 3.2 JDK动态代理
- JDK动态代理是基于接口的动态代理
- 动态代理实现主要分为两个步骤
    - 接口方法的调用使用InvocationHandler接口下的invoke(Object proxy, Method method, Object[] args)方法
    - 代理对象的创建使用Proxy.newProxyInstance(ClassLoader,InterfaceList,InvocationHandler)
        - loader:　　    一个ClassLoader对象，定义了由哪个ClassLoader对象来对生成的代理对象进行加载
        - InterfaceList:　　表示的是我将要给我需要代理的对象提供一组什么接口，如果我提供了一组接口给它，那么这个代理对象就宣称实现了该接口(多态)，这样我就能调用这组接口中的方法了
        - InvocationHandler:　　      一个InvocationHandler对象，表示的是当我这个动态代理对象在调用方法的时候，会关联到哪一个InvocationHandler对象上
- 通用方法（工具类的写法）

```
public class ProxyInvocationHandler implements InvocationHandler {
    private Object target;
    public void setTarget(Object target) {
        this.target = target;
    }
    //生成代理类
    public Object getProxy(){
        return Proxy.newProxyInstance(this.getClass().getClassLoader(),
                target.getClass().getInterfaces(),this);
    }
    // proxy : 代理类
    // method : 代理类的调用处理程序的方法对象.
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //MethodBefore
        Object result = method.invoke(target, args);
        //MethodAfter
        return result;
        
    }
}
```
- 拿到的代理对象是一个抽象接口类型的：这是因为这个代理对象强制类型转化为接口List中的任意一个
```
    public void test() {
        DynamicProxy dynamicProxy = new DynamicProxy();
        dynamicProxy.setTarget(new StaticServiceImpl());
        StaticService proxy = (StaticService) dynamicProxy.getProxy();
        proxy.del();
        proxy.add();
        // 实现另一个接口
        StaticServiceNew proxyNew = (StaticServiceNew) dynamicProxy.getProxy();
        proxyNew.query();
    }
```
# 3. AOP
- Aspect Orient Programming 面向切面编程
- 目的是在不修改源代码的前提下给程序动态增加额外功能的一种技术
- 让spring框架能够非侵入式的加入事务、日志、检测、权限等功能
- 一些专业术语：
    - 横切关注点：跨越不同模块的功能方法（日志、安全、缓存、事务、权限等）
    - Aspect切面：模块化的横切关注点的实现，即一个类（例如一个日志类）
    - Joinpoint连接点：程序中可以被切面插入的点，可以是方法的调用，或是执行过程中的某个时间
    - Pointcut切入点：实际切入的点，连接点的一个子集，实际上就是作用域
    - Advice通知：就是切面类中具体加入的方法，按照加入的位置和阶段可以分为不同的类型
![image](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/04/13/kuangstudyf12770a8-cd09-453f-a995-fe6b22c42a0c.png)

## 3.1 接口+XML配置实现
- 实现通知类（Advice），继承几种通知类型的接口

```
public class Log implements MethodBeforeAdvice {
    @Override
    public void before(Method method, Object[] objects, Object o) throws Throwable {
        System.out.println(o.getClass().getName()+"的"+method.getName() + "方法被执行了" );
    }
}
```
- 在xml中配置织入
    - 将切面类与需要织入的类注册成bean
    - aop-xml配置：`<aop:config>`
        - `<aop:pointcut> `定义一个切点
        - `<aop:advisor>`定义在什么切点需要使用什么advice方法

```
    <aop:config>
        <!--切入点  expression:表达式匹配要执行的方法-->
        <aop:pointcut id="pointcut" expression="execution(* com.robin.service.UserServiceImpl.*(..))"/>
        <!--执行环绕; advice-ref执行方法 . pointcut-ref切入点-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="exceptionLog" pointcut-ref="pointcut"/>
    </aop:config>
```
## 3.2 通过自定义类来实现aop
- 定义业务接口，编写业务实现类
- 使用自定义类实现切面类

```
public class MyPointCut {
    public void after() {
        System.out.println("方法执行后...");
    }
```
- 在xml中配置
    - 定义切入点
    - 通知类型-切入点-通知方法
- 比较简单，但功能没有使用spring API强大（API内置了方法名，接口名等变量）

```
<!--第二种方式自定义实现-->
<!--注册bean-->
<bean id="diy" class="com.kuang.config.DiyPointcut"/>
<!--aop的配置-->
<aop:config>
    <!--第二种方式：使用AOP的标签实现-->
    <aop:aspect ref="diy">
        <aop:pointcut id="diyPonitcut" expression="execution(* com.kuang.service.UserServiceImpl.*(..))"/>
        <aop:before pointcut-ref="diyPonitcut" method="before"/>
        <aop:after pointcut-ref="diyPonitcut" method="after"/>
    </aop:aspect>
</aop:config>
```


## 3.3 通过注解实现aop
- 用到切入面注解`@Aspect`以及几个通知类型注解`@After @Before @Around`
- 首先编写增强实现类
    - 通知类型注解后面要跟该通知的切入点

```
package com.kuang.config;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
@Aspect
public class AnnotationPointcut {
    @Before("execution(* com.kuang.service.UserServiceImpl.*(..))")
    public void before(){
        System.out.println("---------方法执行前---------");
    }
    @After("execution(* com.kuang.service.UserServiceImpl.*(..))")
    public void after(){
        System.out.println("---------方法执行后---------");
    }
    @Around("execution(* com.kuang.service.UserServiceImpl.*(..))")
    public void around(ProceedingJoinPoint jp) throws Throwable {
        System.out.println("环绕前");
        System.out.println("签名:"+jp.getSignature());
        //执行目标方法proceed
        Object proceed = jp.proceed();
        System.out.println("环绕后");
        System.out.println(proceed);
    }
}
```
- 在xml中将其注册为bean

```
<bean id="annotationPointcut" class="com.kuang.config.AnnotationPointcut"/>
<aop:aspectj-autoproxy/>
```


```
通过aop命名空间的<aop:aspectj-autoproxy />声明自动为spring容器中那些配置@aspectJ切面的bean创建代理，织入切面。当然，spring 在内部依旧采用AnnotationAwareAspectJAutoProxyCreator进行自动代理的创建工作，但具体实现的细节已经被<aop:aspectj-autoproxy />隐藏起来了 
<aop:aspectj-autoproxy />有一个proxy-target-class属性，默认为false，表示使用jdk动态代理织入增强，当配为<aop:aspectj-autoproxy  poxy-target-class="true"/>时，表示使用CGLib动态代理技术织入增强。不过即使proxy-target-class设置为false，如果目标类没有声明接口，则spring将自动使用CGLib动态代理。
```

# 4.整合MyBatis
- 一般使用MyBatis-Spring插件

## 4.1 使用SqlSessionFactoryBean-SqlSessionTemplate
- 用bean配置sqlSessionFactory（代替之前在util中配置），并关联mybatis配置
- 注册sqlSessionTemplate，关联sqlSessionFactory；

```
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!--关联Mybatis-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:*.xml"/>
    </bean>
    <!--注册sqlSessionTemplate , 关联sqlSessionFactory-->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <!--利用构造器注入-->
        <constructor-arg index="0" ref="sqlSessionFactory"/>
    </bean>
```

- 增加Dao接口的实现类；private sqlSessionTemplate,增加构造器/set方法，重写接口方法
- 此处要注意原先的mybatis-config就无需注册mapper文件了，相关的配置可以在bean配置通过sqlSessionFactory的property实现

### 4.1.1 使用daoSupport
- 让接口实现类继承SqlSessionDaoSupport
- 这样只需要配置sqlSessionFactory，让实现类的bean直接ref sqlSessionFactory即可
- 省去了对sqlSessionTemplate的处理

## 4.2 使用Spring扫描Mapper文件自动生成bean对象
- 按照以前只定义mapper接口而不用写实现类的写法，直接在service类中引mapper对象，那我们希望能使用bean实现自动注入，例如：
```
public class UserService {    
    @Autowired    
    private UserMapper userMapper;    
    public void insert(User user){        
        userMapper.insert(user); 
    }
}
```
- 此时就引入MapperFactoryBean和MapperScannerConfigurer
    - MapperFactoryBean可以指定mapper接口文件进行扫描
    - 使用 MapperScannerConfigurer为包下的所有接口配置代理对象（建议使用）
        - 还有一些细节，包括扫描自动生成bean name的规则；以及扫描哪些接口来生成bean对象的规则（过滤），可以详细查看文档
```
<!--MapperFactoryBean：用来生成代理对象的工厂类，了解，一般不使用-->
<bean class="org.mybatis.spring.mapper.MapperFactoryBean">
    <property name="mapperInterface" value="cn.dmdream.mybatis.mapper.UserMapper"></property>
    <property name="sqlSessionFactory" ref="sqlSessionFactory"></property>
</bean>
```

```
<!-- MapperScannerConfigurer：通过扫描的模式，扫描目录在cn.dmdream.mybatis.mapper目录下的mapper;映射文件的位置是通过sqlSessionFactory拿到的 -->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="cn.dmdream.mybatis.mapper"></property>
</bean>

```
## 4.3 事务管理
- 利用注解管理：
    - 在业务bean上加@Transcation
- 利用AOP特性
    - tx:advice

    


