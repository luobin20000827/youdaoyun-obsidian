---
created: 2024-05-20
updated: 2024-05-29
source: 有道云笔记迁移
tags:
  - 学习笔记
  - JavaWeb
---

# 第四章 Servlet

## 1.Servlet简介
- 一套运行在服务端 能够响应浏览器请求并返回动态资源的技术规范

## 1.1 Tomcat（Servelet容器）与后端程序交互的流程
- Tomcat将请求转化为HttpServletRequest对象，同时创建了一个代表响应报文的HttpServletResponse对象
- Tomcat根据请求路径找到servlet并实例化，调用service方法，并将先前两个对象传入
- 而servlet里面的service方法会将这两个对象作为入参，处理请求并构建响应。
- Servlet处理完请求后，控制权返回给Tomcat。然后Tomcat从HttpServletResponse对象中获取响应信息，生成HTTP响应并发送给客户端。

## 2.Servelet开发流程

### 2.1 Service jar包导入
- 依赖在导入时优先在pom中配置（如果使用tomcat配置时导入的jar包也能运行，但在package时会出现问题）
- 像servlet这样的jar在编码时需要，但是在运行时有tomcat提供，因此在package不用带入

```
    <dependency>
      <groupId>jakarta.servlet</groupId>
      <artifactId>jakarta.servlet-api</artifactId>
      <version>5.0.0-M1</version>
      <scope>provided</scope> 这个scope就会让package时不会带进servlet
    </dependency>
```


### 2.2 Service()重写
```
public class UserServlet  extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取请求中的参数
        String username = req.getParameter("username");
        if("atguigu".equals(username)){
            //通过响应对象响应信息
            resp.getWriter().write("NO");
        }else{
            resp.getWriter().write("YES");
        }

    }
}
```
- 我们自定义的servlet类都是继承自HttpServlet类
- 在service方法中重写我们需要的业务逻辑
- 与前端的交互依赖Tomcat对Service对象在HttpServletRequest和HttpServletResponse两个报文对象上的操作实现
- 在操作response报文对象时，我们还应该指明返回报文的ContentType告诉浏览器应该将我们的报文对象当作什么类型来解析（默认为text/html，当然也可以设为text/plain）

### 2.3 web.xml的配置
- 上面这个过程的配置在web.xml实现
- 整个路线是从url-name-class实现的
- url还有一些通配符配置（用的不多）
    - / 表示通配所有资源,不包括jsp文件
    - /* 表示通配所有资源,包括jsp文件
    - /a/* 匹配所有以a前缀的映射路径
    - *.action 匹配所有以action为后缀的映射路径

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">

<!-- -class告诉Tomcat具体要反射调用service方法的的Servlet对象的具体路径-->
<!-- -name是给他起一个名字-->

    <servlet>
        <servlet-name>userServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.UserServlet</servlet-class>
    </servlet>

<!-- -url-pattern告诉Tomcat要访问该servlet的路径-->
    <servlet-mapping>
        <!--关联别名和映射路径-->
        <servlet-name>userServlet</servlet-name>
        <!--可以为一个Servlet匹配多个不同的映射路径,但是不同的Servlet不能使用相同的url-pattern-->
        <url-pattern>/userServlet</url-pattern>
       <!-- <url-pattern>/userServlet2</url-pattern>-->
        <!--
            /        表示通配所有资源,不包括jsp文件
            /*       表示通配所有资源,包括jsp文件
            /a/*     匹配所有以a前缀的映射路径
            *.action 匹配所有以action为后缀的映射路径
        -->
       <!-- <url-pattern>/*</url-pattern>-->
    </servlet-mapping>

</web-app>
```
- 以上的过程都可以用@WebServlet(name,urlpattern)简化(代替web.xml配置)
- 修改html文件在tomcat上部署是由滞后性的（浏览器缓存的原因，解决可见`https://blog.csdn.net/weixin_43715214/article/details/122709818`）

## 3.Servelet生命周期

- 一个Servelet对象大致分为四个阶段：
    - 构造器：在对Servelet的第一次请求发出后/容器创建时执行（取决于配置，例如@WebServlet中的loadonStartup default -1(不预启动)，设置该servlet预启动的order(建议从6开始设置，越小越早)）
    - 初始化init()：同上
    - 执行service()：后续每一次向servlet发送请求调用service()方法时都属于这个阶段
    - 销毁destory()：容器停止时触发
- 除了service()外其他步骤都只会执行一次
- Servlet在Tomcat内是单例模式，不建议在里面新增成员变量，不然可能会有线程安全问题


## 4.Servelet继承结构
![image](https://img-blog.csdnimg.cn/img_convert/826e43768bc7ece8a981f76f9d338cf7.png)
- 


## 5.ServletConfig和ServletContext

### 5.1 ServletConfig

```
package jakarta.servlet;
import java.util.Enumeration;
public interface ServletConfig {
    String getServletName();
    ServletContext getServletContext();
    String getInitParameter(String var1);
    Enumeration<String> getInitParameterNames();
}
```

- 用来获取web.xml中改servelet的配置信息
    - 也可以在WebServlet中配置

```
@WebServlet(urlPatterns = "/myServlet", initParams = {
    @WebInitParam(name = "configParam1", value = "Value1"),
    @WebInitParam(name = "configParam2", value = "Value2")
})
public class MyServlet extends HttpServlet {
    //...
}
```

### 5.2 ServletContext

![image](https://github.com/luobin7/atguigu-javaweb/raw/main/images/1682303205351.png)

- 共享的ServletContext

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">

    <context-param>
        <param-name>paramA</param-name>
        <param-value>valueA</param-value>
    </context-param>
    <context-param>
        <param-name>paramB</param-name>
        <param-value>valueB</param-value>
    </context-param>
</web-app>
```
- 项目的上下文路径/真实路径也可以通过api拿到
- 域对象
    - `void setAttribute(String key,Object value);`
    - `Object getAttribute(String key);`
    - `void removeAttribute(String key);`

## 5.HttpServletResponse和HttpServletRequest
- 两个接口都有一些API对出入的报文对象进行规范
- 这两个接口都是由Tomcat预先创建的，将请求报文和响应报文封装得到的

### 5.1 HttpServletRequest常见API
- 获取请求行信息：getReuqestXXX()
- 获取请求头信息: getHeaderXXX()/getContentType()
- 获取请求参数: getParameter()/getParameterValues()/BufferedReader  getReader()/ServletInputStream getInputStream() 

### 5.2 HttpServletResponse常见API

- 基本api用法同req相同
- 重点是响应体：getWriter()/getOutputStream()/setContentLength()

## 6.请求转发和响应重定向
- Servlet处理页面跳转的两种手段

### 6.1 请求转发
![image](https://github.com/luobin7/atguigu-javaweb/blob/main/images/1682321228643.png?raw=true)
- 请求转发通过HttpServletRequest对象获取请求转发器实现，全称只有一个req，因此请求中的参数也是一起传递的
- Tomcat访问应用根目录默认展示的页面是由web.xml中welcome_file中定义的（如果没有显示的指明的话，tomcat会优先寻找"index.html/htm/jsp"的同名文件）
- 请求转发操作对客户端是屏蔽的，因此整个过程中的url是不会变的
- 不仅可以转发req给其他Servlet处理，同样也可以重新转发到其他资源（Web-INF中受保护的资源文件也可以，这是访问这些资源文件的唯一方法）
- 请求转发不能访问外部的资源

```
@WebServlet("/servletA")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //  获取请求转发器
        //  转发给servlet  ok
        RequestDispatcher  requestDispatcher = req.getRequestDispatcher("servletB");
        //  转发给一个视图资源 ok
        //RequestDispatcher requestDispatcher = req.getRequestDispatcher("welcome.html");
        //  转发给WEB-INF下的资源  ok
        //RequestDispatcher requestDispatcher = req.getRequestDispatcher("WEB-INF/views/view1.html");
        //  转发给外部资源   no
        //RequestDispatcher requestDispatcher = req.getRequestDispatcher("http://www.atguigu.com");
        //  获取请求参数
        String username = req.getParameter("username");
        System.out.println(username);
        //  向请求域中添加数据
        req.setAttribute("reqKey","requestMessage");
        //  做出转发动作
        requestDispatcher.forward(req,resp);
    }
}
```


### 6.2 响应重定向
![image](https://github.com/luobin7/atguigu-javaweb/blob/main/images/1682322460011.png?raw=true)
- 使用resp.sendRedirect(String s)实现
- 不同的是可以访问外部的web资源
- 两种方式优先使用响应重定向
- 302临时重定向，301永久重定向


## 7.Web乱码与路径

### 7.1 乱码问题
- 可能出现的乱码
    - HTML乱码：头文件中设置
    - 本地log乱码：修改本地软件的的log_conf编码
    - 浏览器端乱码：设置resp的contentType让他按照我们规定的解码方式解码
    
### 7.2 路径问题

- 相对路径
    - Web项目整个资源项目的根目录是从webapp module开始的
    - 相对路径是以当前资源**所在**的位置（在的那个文件夹）为出发点去寻找目标资源
    - 不以`'/'`开头  `'./'`是当前路径，一般省略;   `'../'`是上层路径
- 绝对路径
    - 绝对路径以`"/"`开头
    - 绝对路径的基准路径始终是整个Web项目的根目录
- 相对路径与绝对路径间的转换
    - 能通过html_head标签中的base herf标签转换（会自动在相对路径前拼接herf内的内容）
- 在请求转发中，要注意浏览器端的url是不变的，因此资源的访问在按照相对路径设置时还是要按照改变后转发到的Servlet的url中设置
- 在生产中更常见的做法是一个tomcat就运行一个项目（通过端口号控制），此时就不用考了上下文了

## 3.MVC架构模式
- M-model 控制数据模型与业务逻辑
    - dao：封装数据操作
    - domain：数据实体
    - service：协调dao和domain处理业务逻辑（有人放到C层）
- V：前端
- C：连接前后端的controller
- 下面我们用一个日程管理的demo来练习这些概念

### 3.1 数据实例Pojo

- Lombok的使用：快速构建entity类（getter/setter方法，hash/equals方法，无参构造）（前置：plugins/jar/annotationprocessors勾选）
    - @DATA
    - @NoArgsConstructor

### 3.2 DAO层封装
- Basedao
    - 单对象查询（类似count(1)）
        - 注意count(*)这种返回的哦都是long型
    - 多对象查询（常规的查询列表）
    - update方法（增删改查）
- 接口类：定义增删改查方法
- Impl实现类:实现接口中的方法

### 3.3 Service层封装
- 围绕数据库中的表实现功能
- 接口
- 实现类

### 3.3 Controller层封装
- 控制与前后端通过信
- 同样可以封装Base Controller
- 前端input类在请求中返回的参数的名字由他们的name属性决定
- 实现登录和注册功能时出现的两个问题
    - 数据库字段名
    - 引用数据类型的对比用equals()而不用==


## 相关笔记
所属索引：[[JavaWeb-MOC]]
