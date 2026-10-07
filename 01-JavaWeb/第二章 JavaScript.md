---
created: 2024-05-13
updated: 2024-05-20
source: 有道云笔记迁移
---

# 1.JS简介

## 1.1 JS特点
- JS是一门解释型的脚本语言（py）
- 基于对象的脚本语言，三大特性种不继承多态，因此也不算面向对象
- 弱类型
- 事件驱动
- 跨平台性

## 1.2 JS组成部分
![image](https://github.com/luobin7/atguigu-javaweb/raw/main/images/1681266220955.png)

## 1.3 BOM和DOM
- BOM就是将浏览器窗口windows抽象成各个对象，通过各个对象的API操作组件行为
![image](https://github.com/luobin7/atguigu-javaweb/raw/main/images/1681267483366.png)
- DOM就是用document对象的API完成对网页解析过后的HTML文档的修改以实现网页样式的动态修改
![image](https://github.com/luobin7/atguigu-javaweb/raw/main/images/1681269970254.png)
![image](https://github.com/luobin7/atguigu-javaweb/raw/main/images/1681270260741.png)

## 1.4 JS的引入方式
- JS在html页面种通过一对script标签引入的
- 更常用的通过外部脚本的方式引入的
    - 在HTML文件中使用<script>标签，通过src属性引入外部的.js文件`。<script src="script.js"></script>`

# 2.JS的数据类型与运算符

## 2.1 JS的数据类型
- 数据类型统一为number，不分整型和浮点型
- 字符串类型为string，不严格区分单双引号
- 布尔值为boolean，注意：在if结构中非空字符与非零字符都是true
- 引用数据类型都是Object
- 函数在JS中属于function类型
- 弱类型JS中的变量只有在被赋值时才确定数据类型，因此没赋值时都是undefined
- null值时Object，判断数据类型可以用typeof
- 可以统一声明位var类型

## 2.2 JS的运算符
- 算数运算符和Java基本一样，除了在/与%运算时有一些不同
- 关系运算符 ==在遇到数据类型不一致时会试着将比较的数据转为number再比较；===则会直接返回false

## 2.3 JS的流程控制与函数
- 循环、分支几乎都与Java一致
- 函数声明和python比较类似

```
/* 
语法1 
    function 函数名 (参数列表){函数体}
            */
function sum(a, b){
    return a+b;
}
var result =sum(10,20);
console.log(result)

/* 
语法2
    var 函数名 = function (参数列表){函数体}
            */
var add = function(a, b){
    return a+b;
}
var result = add(1,2);
console.log(result);
```

# 3.JS中的面向对象和JSON

## 3.1 JS中创建Object

- JS中创建对象可以使用Java中的Class-instance写法
- 同时，JS还可以使用对象字面量语法的写法，直接new Object或者{}定义类

```
var person =new Object();
// 给对象添加属性并赋值
person.name="张小明";
person.age=10;
person.foods=["苹果","橘子","香蕉","葡萄"];
// 给对象添加功能函数
person.eat= function (){
    console.log(this.age+"岁的"+this.name+"喜欢吃:")
    for(var i = 0;i<this.foods.length;i++){
        console.log(this.foods[i])
    } 
}
//获得对象属性值
console.log(person.name)
console.log(person.age)
//调用对象方法
person.eat();
```

```
var person ={
    "name":"张小明",
    "age":10,
    "foods":["苹果","香蕉","橘子","葡萄"],
    "eat":function (){
        console.log(this.age+"岁的"+this.name+"喜欢吃:")
        for(var i = 0;i<this.foods.length;i++){
            console.log(this.foods[i])
        } 
    }
}
//获得对象属性值
console.log(person.name)
console.log(person.age)
//调用对象方法
person.eat();
```

## 3.2 JSON格式
- JSON,全称JavaScript Object Notation
- 主要由键值对(Object,即{})和数组(Arrays,即[])组成（可以只看做是一个由JS字面量语法创建的对象）

```
/* 定义一个JSON串 */
var personStr ='{"name":"张小明","age":20,"girlFriend":{"name":"铁铃","age":23},"foods":["苹果","香蕉","橘子","葡萄"],"pets":[{"petName":"大黄","petType":"dog"},{"petName":"小花","petType":"cat"}]}'
console.log(personStr)
console.log(typeof personStr)
/* 将一个JSON串转换为对象 */
var person =JSON.parse(personStr);
console.log(person)
console.log(typeof person)
/* 获取对象属性值 */
console.log(person.name)
console.log(person.age)
console.log(person.girlFriend.name)
console.log(person.foods[1])
console.log(person.pets[1].petName)
console.log(person.pets[1].petType)
```
- JSON -> JS中的对象使用JSON.parse()
- JS中的对象 -> JSON 使用JSON.stringify()
- JSON -> JS中的对象 使用JSON.parse()
![image](https://github.com/luobin7/atguigu-javaweb/raw/main/images/1681292306466.png)
- 在后端我们需要使用反射技术实现JSON与字符串的互转，一般使用包装好的方法（Jackson，Gson等等）

## 3.3 JS常见对象
### 3.3.1 数组
- 属于Object类型，变长，比较类似Java中的ArrayList

### 3.3.2 Boolean对象
- 提供了toString()和valueOf()
- 不建议使用，建议直接使用Boolean

### 3.3.3 String对象
- 用法类似Java

### 3.3.34 其他常用对象
- Math对象
- Date对象

# 4.事件

- 事件可以是浏览器行为，也可以是用户行为。我们可以设计一些JS函数由事件驱动。

## 4.1 常见的事件
- 鼠标事件：click，dblclick，mousedown，mouseup，mouseover，mouseout，mousemove 等
- click，dblclick，mousedown，mouseup，mouseover，mouseout，mousemove 等
- 表单事件（Form Events）：如 submit，change，focus，blur 等
- 窗口事件（Window Events）：如 load，resize，scroll，unload 等

## 4.2 事件的绑定
- 通过元素的属性、

```
    <head>
        <meta charset="UTF-8">
        <title>小标题</title>
      
        <script>
            function testDown1(){
                console.log("down1")
            }
            function testDown2(){
                console.log("down2")
            }
            function testFocus(){
                console.log("获得焦点")
            }

            function testBlur(){
                console.log("失去焦点")
            }
            function testChange(input){
                console.log("内容改变")
                console.log(input.value);
            }
            function testMouseOver(){
                console.log("鼠标悬停")
            }
            function testMouseLeave(){
                console.log("鼠标离开")
            }
            function testMouseMove(){
                console.log("鼠标移动")
            }
        </script>
    </head>

    <body>
        <input type="text" 
        onkeydown="testDown1(),testDown2()"
        onfocus="testFocus()" 
        onblur="testBlur()" 
        onchange="testChange(this)"
        // 在方法中可以传入this对象表示自己
        onmouseover="testMouseOver()" 
        onmouseleave="testMouseLeave()" 
        onmousemove="testMouseMove()" 
         />
    </body>
```

- 通过DOM编程

```
    <head>
        <meta charset="UTF-8">
        <title>小标题</title>
      
        <script>
            //
           js中的代码都是顺序加载的，因此要先出发页面加载完毕事件,让浏览器扫描完所有的元素
            window.onload=function(){
                var in1 =document.getElementById("in1");
                // 通过DOM编程绑定事件
                in1.onchange=testChange
            }
            function testChange(){
                console.log("内容改变")
                console.log(event.target.value);
            }
        </script>
    </head>

    <body>
        <input id="in1" type="text" />
    </body>
```

# 5.BOM编程

## 5.1 对BOM的认识
- BOM，Browser Objct Mode
- 整个Browser内的结构：
    - window顶级对象，代表整个浏览器窗口
        - location对象 window对象的属性之一,代表浏览器的地址栏
        - history对象 window对象的属性之一,代表浏览器的访问历史
        - screen对象 window对象的属性之一,代表屏幕
        - navigator对象 window对象的属性之一,代表浏览器软件本身
        - document对象 window对象的属性之一,代表浏览器窗口目前解析的html文档
        - console对象（FN+F12可以进入开发控制台查看） window对象的属性之一,代表浏览器开发者工具的控制台
        - localStorage对象 window对象的属性之一,代表浏览器的本地数据持久化存储
        - sessionStorage对象 window对象的属性之一,代表浏览器的本地数据会话级存储
    

## 5.2 window对象的常见属性与方法

1.弹窗方式：
- alert：信息提示
- confirm：信息确认
- prompt：信息输入

2.延时
- window.setTimeout(function(){})

3.前后跳转
- history.back(n)
- history.go(n)

4. 跳转到特定url
- location.href = "xxxxxx.com"

5.存储数据
SessionStorage.setItem(key:value)
LocalStorage.setItem(k:v)

# 6. DOM编程

## 6.1 对DOM和document对象的认识
- DOM编程其实就是用window对象的document属性的相关API完成对页面元素的控制的编程
- 利用 DOM，我们可以通过 JavaScript 动态地改变浏览器正在展示的页面的内容和结构，而不需要每次变动都重新加载页面。
- 这种document是一种树形结构，由层叠的node组成（以下结点类型的父类型）
    - 元素节点element
    - 属性节点attribute
    - 文本节点text
![image](https://github.com/luobin7/atguigu-javaweb/raw/main/images/1681269970254.png)

## 6.2 获取页面元素的几种方式
- 元素节点：document.getElementByXx(Id/Name/TagName/ClassName)
- 间接获取子/父/兄弟节点：element.children（此时获取的是一个element[],可以通过数组拿到里面特定的子元素）/firstElementChild/lastElementChild/parentElement/previousElementSibling/nextElementSibling

```

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
   <script>
    /* 
    1 获得document  dom树
        window.document
    2 从document中获取要操作的元素
        1. 直接获取
            var el1 =document.getElementById("username") // 根据元素的id值获取页面上唯一的一个元素
            var els =document.getElementsByTagName("input") // 根据元素的标签名获取多个同名元素
            var els =document.getElementsByName("aaa") // 根据元素的name属性值获得多个元素
            var els =document.getElementsByClassName("a") // 根据元素的class属性值获得多个元素
        2. 间接获取
            var cs=div01.children // 通过父元素获取全部的子元素
            var firstChild =div01.firstElementChild  // 通过父元素获取第一个子元素
            var lastChild = div01.lastElementChild   // 通过父元素获取最后一个子元素
            var parent = pinput.parentElement  // 通过子元素获取父元素
            var pElement = pinput.previousElementSibling // 获取前面的第一个元素
            var nElement = pinput.nextElementSibling // 获取后面的第一个元素
    3 对元素进行操作
        1. 操作元素的属性
        2. 操作元素的样式
        3. 操作元素的文本
        4. 增删元素   
    */
   function fun1(){
        //1 获得document
        //2 通过document获得元素
        var el1 =document.getElementById("username") // 根据元素的id值获取页面上唯一的一个元素
        console.log(el1)
   }
   function fun2(){
        var els =document.getElementsByTagName("input") // 根据元素的标签名获取多个同名元素
        for(var i = 0 ;i<els.length;i++){
            console.log(els[i])
        }
   }
   function fun3(){
        var els =document.getElementsByName("aaa") // 根据元素的name属性值获得多个元素
        console.log(els)
        for(var i =0;i< els.length;i++){
            console.log(els[i])
        }
   }

   function fun4(){
    var els =document.getElementsByClassName("a") // 根据元素的class属性值获得多个元素
    for(var i =0;i< els.length;i++){
            console.log(els[i])
        }
   }

   function fun5(){
    // 先获取父元素
     var div01 = document.getElementById("div01")
     // 获取所有子元素
     var cs=div01.children // 通过父元素获取全部的子元素
     for(var i =0;i< cs.length;i++){
            console.log(cs[i])
     }

     console.log(div01.firstElementChild)  // 通过父元素获取第一个子元素
     console.log(div01.lastElementChild)   // 通过父元素获取最后一个子元素
   }

   function fun6(){
        // 获取子元素
        var pinput =document.getElementById("password")
        console.log(pinput.parentElement) // 通过子元素获取父元素
   }

   function fun7(){
        // 获取子元素
        var pinput =document.getElementById("password")
        console.log(pinput.previousElementSibling) // 获取前面的第一个元素
        console.log(pinput.nextElementSibling) // 获取后面的第一个元素
   }
   </script>
</head>
<body>
    <div id="div01">
        <input type="text" class="a" id="username" name="aaa"/>
        <input type="text" class="b" id="password" name="aaa"/>
        <input type="text" class="a" id="email"/>
        <input type="text" class="b" id="address"/>
    </div>
    <input type="text" class="a"/><br>

    <hr>
    <input type="button" value="通过父元素获取子元素" onclick="fun5()" id="btn05"/>
    <input type="button" value="通过子元素获取父元素" onclick="fun6()" id="btn06"/>
    <input type="button" value="通过当前元素获取兄弟元素" onclick="fun7()" id="btn07"/>
    <hr>

    <input type="button" value="根据id获取指定元素" onclick="fun1()" id="btn01"/>
    <input type="button" value="根据标签名获取多个元素" onclick="fun2()" id="btn02"/>
    <input type="button" value="根据name属性值获取多个元素" onclick="fun3()" id="btn03"/>
    <input type="button" value="根据class属性值获得多个元素" onclick="fun4()" id="btn04"/>
    
</body>
</html>
```

## 6.3 对元素进行修改
1. 属性操作
2. 内部文本操作
- element.innerText
- element.innerHTML
3. 增删操作
- document.createElement(“标签名”)
- document.createTextNode(“文本值”)//这两个只是完成了对节点的创建，要在文档中可见还必须使用下面的方法实现插入
- element.appendChild(ele)
- parentEle.insertBefore(newEle,targetEle)
- parentEle.replaceChild(newEle, oldEle)
- element.remove()

# 7.正则表达式
- JS种的正则表达式格式：`/需要匹配的格式pattern/表达匹配模式的修饰符modifier`
- 修饰符常用的有`i`表示忽略大小写；g表示全局匹配（默认的是在找到第一个匹配后就停止）
- 具体的匹配模式可以查阅文档
- 正则表达一般用reg命名，主要用到的方法有四个：
    - reg.test(str) 验证是否包含给定的正则表达式(调用正则表达式的方法)
    - str.match(reg) 按照规定的模式进行匹配
    - str.replace(reg,'要替换的内容')

```
// 目标字符串
var targetStr = 'Hello World!';

// 全局匹配所有首字母大写的英文单词
var reg = /\b[A-Z][a-z]+\b/g;
// 获取全部匹配
var resultArr = targetStr.match(reg);
// 数组长度为2
console.log("resultArr.length="+resultArr.length);
// 遍历数组，发现只能得到'Hello'和'World'
for(var i = 0; i < resultArr.length; i++){
  console.log("resultArr["+i+"]="+resultArr[i]);
}
```
- 一些常用的正则表达式
    - 用户名：	`/^[a-zA-Z ][a-zA-Z-0-9]{5,9}$/`
    - 密码：`/^[a-zA-Z0-9 _-@#& *]{6,12}$/`
    - 电子邮箱：`/^[a-zA-Z0-9 _.-]+@([a-zA-Z0-9-]+[.]{1})+[a-zA-Z]+$/`