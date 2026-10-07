---
created: 2024-07-02
updated: 2024-07-11
source: 有道云笔记迁移
tags:
  - 学习笔记
  - SSM
---

# 1. MVC回顾
![image](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/04/13/kuangstudy5989b959-a64d-4469-952f-d23699b0bad7.png)

# 2. SpringMVC

## 2.1 DispatcherServlet
- SpringWeb框架围绕DispatcherServelt[调度Servlet]设计，是前端控制器设计模式的实现，正是这个核心组件接收所有传输到Web应用的HTTP请求。
- DispatcherServlet的作用
    - 把一个HTTPrequest交给它真正的处理方法
    - 解析HTTP request的header和body中的数据，并把它们转换为DTO(数据传输对象)
    - Model-View-Controller三方的交互
    - 再把业务逻辑返回的DTO转换成HTTP response
    - 渲染具体的视图等
    
![image](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/04/13/kuangstudy00854e07-7eac-476c-a9dd-dcebb7ac0b89.png)

## 2.2 SpringMVC执行原理
![image](https://img-blog.csdnimg.cn/c1063d2b9bc7453e813e781bf1618468.png)
- `"http://localhost:8080/SpringMVC/hello"`
    - SpringMVC表示部署在服务器（Tomcat）上的web站点
    - hello就表示处理器handler-此处就等价为我们自己设计的controller
    - 处理器映射器HandlerMapping由框架提供，自动解析url和method来找到对应的handler
    - 处理器适配器HandlerAdapter作用就是根据HandlerMapping所提供的Handler信息，会按照特定的规则去执行相关的处理器Handler
    - 围绕整个按照前端请求找对应的controller就对应了活找工具（HandlerMapping）-工具找人（HandlerAdapter）-人拿工具干活（Controller

## 2.3 编码流程
### 2.3.1 xml开发
- 在web.xml中注册ServletDispatcher，让其关联spring配置xml
- 在springxml中配置HandlerAdapter, HandlerMapping以及视图解析器

### 2.3.2 注解开发
- 配置
- 在web.xml中只需要配置前端控制器xml(bug:DisPatcherServlet依赖是javax，不适配tomcat10以上用的jakarta，必须降级回tomcat9)
- 添加视图解析器的bean
- 编写controller（继承controller接口（不推荐）/使用注解）（前后端参数的交互可以使用modelvew）
    - 当返回字符串时，return的结果会被视图解析器捕获（排除foward重定向等情况），按照规则拼接后寻找对应的视图文件
- 连接前端

## 2.4 控制器Controller

### 2.4.1 常用的注解
- @Controller 注册为控制器
- @RequestMapping("/url") 方法和类上都能使用，映射请求路径和处理请求的方法/类
    - 变体：@方法Mapping
- @RequestParam：从请求url的参数中中拿到参数的值（&连接的值）

- @PathVariable：从请求url路径中拿到参数值
    - 通过@PathVariable，例如`/blogs/1`
    - 通过@RequestParam，例如`blogs?blogId=1`
```
@RequestMapping(value = {"/selectPaperDatum}"}, method = RequestMethod.GET)
    public ItooResult selectPaperDatum(@RequestParm("studentId") String studentId) {
    }
对应的URL:http://localhost:8084/.../selectPaperDatum?studentId=123

@RequestMapping(value = "copyPaperByPaperId/{paperId}/{paperName}", method = RequestMethod.POST)
public ItooResult copyPaperByPaperId(@PathVariable String paperId, @PathVariable String paperName) {
    }
对应的url：http://localhost:8084/papersManager/copyPaperByPaperId/123/测试
```

### 2.4.2 转发与重定向
- 除了使用拼接规则，也可以直接返回视图软件
- 重定向forward
- 转发redirect

```
    @RequestMapping("forwardHandler01")
    public String forwardHandler01(){
        return "forward:/success.jsp";
    }
    @RequestMapping("/forwardHandler02")
    public String forwardHandler02(){
        return "forward:/forwardHandler01";
    }
    @RequestMapping("/redirectHandler01")
    public String redirectHandler01(){
        return "redirect:/success.jsp";
    }
 
    @RequestMapping(value="/redirectHandler02")
    public String redirectHandler02(){
        return "redirect:/redirectHandler01";
    }
```
### 2.4.3 前后端传参
- 前端传后端的写法见2.4.1
    - 对象也可以传

```
//提交数据 : http://localhost:8080/mvc04/user?name=kuangshen&id=1&age=15
@RequestMapping("/user")
public String user(User user){
    System.out.println(user);
    return "hello";
}
```

- 后端传前端
1. 通过modelview、ModelMao、Model

```
public class ControllerTest1 implements Controller {
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        //返回一个模型视图对象
        ModelAndView mv = new ModelAndView();
        mv.addObject("msg","ControllerTest1");
        mv.setViewName("test");
        return mv;
    }
}
```


### 2.4.4 @ResponseBody
- 之前在使用@Controller时，方法的返回值默认为一个模型视图model，servlet在拿到model后就会按照规则找到对应的视图进行渲染
- 但有些时候我们想让方法的返回值就作为响应的主体内容，而不是解析为视图
- 此处引入的@ResponseBody就起到这个作用，告诉 Spring MVC 框架将方法的返回值序列化为特定格式（如 JSON、XML 等）并作为响应的主体内容返回给客户端。
    - @Controller + @ResponseBody = @RestController

## 2.5 JSON
- 书接上文，使用@ResponseBody返回JSON时我们可以使用一些线程的工具
- jackson/fastjson
- 时间格式处理

## 2.6 拦截器
- 继承HandleInterceptor类
    - 这个接口包含了三个方法：preHandle、postHandle、afterCompletion
    - preHandle：处理器执行之前执行，如果返回 false 将跳过处理器、拦截器 postHandle 方法、视图渲染等，直接执行拦截器 afterCompletion 方法。
    - postHandle：处理器执行后，视图渲染前执行，如果处理器抛出异常，将跳过该方法直接执行拦截器 afterCompletion 方法。
    - afterCompletion：视图渲染后执行，不管处理器是否抛出异常，该方法都将执行。

## 相关笔记
所属索引：[[SSM-MOC]]
- [[Spirng]] — IoC 与 AOP 基础
- [[MyBatis]] — 持久层框架
