---
created: 2024-05-11
updated: 2024-05-20
source: 有道云笔记迁移
---

# 前端概述
![image](https://github.com/luobin7/atguigu-javaweb/raw/main/images/1681177316138.png)

# 第一章 前端入门：HTML&CSS

## 1.HTML入门

### 1.1 三者的主要作用
- HTML一般用于搭建网站主体
- CSS一般用于页面元素美化
- JavaScript一般用于页面动态元素的处理

### 1.2 HTML是什么
- 超文本标记语言
- 可以类比markdown，也是一门标记语言
- 本质是文本文件，但能通过结合标签将图片、音视频等多种媒体资源引入网页中

### 1.3 标签
- HTML的元素中都是用标签来定义的
- 主要有两种：双标签与单标签（自闭和/空元素标签）
    - 双标签：开始标签，结束标签，包含元素内容在中间
    - 单标签：例如，<br>（换行符）、<hr>（水平线）、<img>（图像）、<input>（输入字段）等就是单标签。

```
//双标签
<p>这是一个段落</p>
<div>这是一个div元素</div>
<span>这是一个span元素</span>
//单标签
<br>
<hr>
<img src="image.jpg" alt="image description">
<input type="text" name="name">
```


### 1.4 基本结构
- 文档类型说明

```
<!DOCTYPE html>
```

- 根标签

```
<html>
```

- 头部元素：包含了所有的元数据元素，例如字符集声明、CSS样式链接、JavaScript文件链接、网页标题等。头部信息不会在浏览器窗口中显示出来，主要用于搜索引擎优化和设置页面样式及行为。

```
<head>
```

- 主体元素：包含了网页的所有可见内容，如段落、图像、链接、表格、列表等。

```
<body>
```

- 注释

```
<!-- 这是一个注释 -->
```
![image](https://github.com/luobin7/atguigu-javaweb/raw/main/images/1681180699132.png)


### 1.5 语法规则
1. 根标签有且只能有一个
2. 无论是双标签还是单标签都需要正确关闭
3. 标签可以嵌套但不能交叉嵌套

4. 注释语法为 ,注意不能嵌套

5. 属性必须有值，值必须加引号,H5中属性名和值相同时可以省略属性值

6. HTML中不严格区分字符串使用单双引号

7. HTML标签不严格区分大小写,但是不能大小写混用

8. HTML中不允许自定义标签名,强行自定义则无效


### 1.6 开发工具
- Vscode
    - GOLIVE插件

## 2.HTML常见标签

### 2.1 标题标签

- 双标签：<h?> </h?>

```
<body>
    <h1>一级标题</h1>
    <h2>二级标题</h2>
    <h3>三级标题</h3>
    <h4>四级标题</h4>
    <h5>五级标题</h5>
    <h6>六级标题</h6>
</body>
```

### 2.2 段落标签
- 双标签：<p> 段落内容 </p>

```
<body>
    <p>
        记者从工信部了解到，近年来我国算力产业规模快速增长，年增长率近30%，算力规模排名全球第二。
    </p>
    <p>
        工信部统计显示，截至去年底，我国算力总规模达到180百亿亿次浮点运算/秒，存力总规模超过1000EB（1万亿GB）。
        国家枢纽节点间的网络单向时延降低到20毫秒以内，算力核心产业规模达到1.8万亿元。中国信息通信研究院测算，
        算力每投入1元，将带动3至4元的GDP经济增长。
    </p>
    <p> 
        近年来，我国算力基础设施发展成效显著，梯次优化的算力供给体系初步构建，算力基础设施的综合能力显著提升。
        当前，算力正朝智能敏捷、绿色低碳、安全可靠方向发展。
    </p>
</body>
```

### 2.3 换行标签
- 单标签：<br> 换行 <hr> 添加分隔线

### 2.4 列表标签
- 有序列表
    - 列表标签 ol
    - 列表项 li

```
<ol>
    <li>JAVA</li>
    <li>前端</li>
    <li>大数据</li>
</ol>
```

- 无序列表
    - 列表标签改为 ul
- 两者可以嵌套使用

### 2.5 超链接标签
- 双标签，也被称为a标签
- 主要有herf和target两个属性
- herf用于定义链接
    - href中可以使用绝对路径,以/开头,始终以一个固定路径作为基准路径作为出发点
    - href中也可以使用相对路径,不以/开头,以当前文件所在路径为出发
    - href中也可以定义完整的URL
    - 也可以用来跳转当当前页面的其他部分
- target标签主要是用来定义打开链接的方式的

```
<body>
    <!-- 
        href属性用于定义连接
            href中可以使用绝对路径,以/开头,始终以一个路径作为基准路径作为出发点
            href中也可以使用相对路径,不以/开头,以当前文件所在路径为出发点
            # 开头跳转到这个页面的 其他部分。 
            href中也可以定义完整的URL
        target用于定义打开的方式
            _blank 在新窗口中打开目标资源
            _self  在当前窗口中打开目标资源
     -->
   <a href="01html的基本结构.html" target="_blank">相对路径本地资源连接</a> <br>
   <a href="/day01-html/01html的基本结构.html" target="_self">绝对路径本地资源连接</a> <br>
   <a href="http://www.atguigu.com" target="_blank">外部资源链接</a>
   <a href="#section1">
   <br>
   
</body>
```

### 2.6 多媒体标签

- 图片
    - 单标签

```
   <!-- 
    src
        用于定义目标声音资源
    autoplay
        用于控制打开页面时是否自动播放
    controls
        用于控制是否展示控制面板
    loop
        用于控制是否进行循环播放
    --> 
   <audio src="img/music.mp3" autoplay="autoplay" controls="controls" loop="loop" />
```

- 音频和视频标签
    - 双标签

```
<body>
   <!-- 
    src
        用于定义目标视频资源
        如果有多个src，他是从上到下寻找直到遇到一个个可以播放的视频
    autoplay
        用于控制打开页面时是否自动播放
    controls
        用于控制是否展示控制面板
    loop
        用于控制是否进行循环播放
    --> 
  <video controls width="400px">
  <source src="movie.mp4" type="video/mp4">
  <source src="movie.ogg" type="video/ogg">
  你的浏览器无法播放此视频。//备用文字
</video>
</body>
```

## 2.7 表格标签
- table标签 代表表格

- thead标签 代表表头 可以省略不写

- tbody标签 代表表体 可以省略不写

- tfoot标签 代表表尾 可以省略不写

- tr标签 代表一行

- td标签 代表行内的一格

- th标签 自带加粗和居中效果的td


```
    <h3 style="text-align: center;">员工技能竞赛评分表</h3>
    <table  border="1px" style="width: 400px; margin: 0px auto;">
        <tr>
            <th>排名</th>
            <th>姓名</th>
            <th>分数</th>
        </tr>
        <tr>
            <td>1</td>
            <td>张小明</td>
            <td>100</td>
        </tr>
        <tr>
            <td>2</td>
            <td>李小东</td></td>
            <td>99</td>
        </tr>
        <tr>
            <td>3</td>
            <td>王小虎</td>
            <td>98</td>
        </tr>
    </table>
```

## 2.8 表单数据
- <form>创建表单，其中可以包含各种各样的输入元素，例如：
    - <input>: 通过input的不同type又用于定义文本输入框、复选框、单选框、按钮等。
    - <textarea>: 用于多行文本输入框。
    - <select>: 用于创建一个下拉列表。
    - <button>: 用于定义一个可点击的按钮。

```
<form action="submit_form.php" method="post">
  姓名：<input type="text" name="name"><br>
  电邮：<input type="email" name="email"><br>
  <input type="submit" value="提交">
</form>
```

- action定义了提交信息的服务器地址
- method则定义了访问的方式，HYML一般支持的是get（只读幂等）和post（创建）

## 2.9 布局标签
- div块标签
- span层标签
- 配合css使用满足页面布局


# 3.CSS的使用
- CSS(Cascading Style Sheets)层叠样式表，一门对元素布局、外观和行为进行控制的语言

## 3.1 CSS引入方式
- 行内式，通过需要应用样式`（例如<p>、<div>、<h1>等等）`的style属性引入, 样式语法为 样式名:样式值; 样式名:样式值;
    - 不直观，复用度也比较低
- 内嵌式：在head标签内通过多对style标签定义不同样式的CSS样式（通过依赖选择器来确定每个CSS样式的作用范围）

```
<head>
    <meta charset="UTF-8">
    <style>
        /* 通过选择器确定样式的作用范围 */
        input {
            display: block;
            width: 80px; 
            height: 40px; 
            background-color: rgb(140, 235, 100); 
            color: white;
            border: 3px solid green;
            font-size: 22px;
            font-family: '隶书';
            line-height: 30px;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <input type="button" value="按钮1"/> 
    <input type="button" value="按钮2"/> 
    <input type="button" value="按钮3"/> 
    <input type="button" value="按钮4"/> 
</body>
```

- 外联式：可以在项目单独创建css样式文件,专门用于存放CSS样式代码
- 需要使用时，在head标签中,通过link标签引入外部CSS样式即可


## 3.2 选择器
- 选择器的切入角度有很多，可以用样式、id、class等方式选择
    - 元素名 {}
    - .class值 {}
    - id选择器 #id值 {}
- 选择范围 大 -> 小

```
input {
        display: block;
        width: 80px; 
        height: 40px; 
        background-color: rgb(140, 235, 100); 
        color: white;
        border: 3px solid green;
        font-size: 22px;
        font-family: '隶书';
        line-height: 30px;
        border-radius: 5px;
    }
```

## 3.3 CSS浮动
- 常用于将文本环绕在图片周围，或者在布局中创建水平的元素，如菜单和导航条。

## 3.4 CSS定位

## 3.5 CSS盒子模型
- CSS盒模型本质上是一个盒子，封装周围的HTML元素，它包括：边距（margin），边框（border），填充（padding），和实际内容（content）
![image](https://github.com/luobin7/atguigu-javaweb/raw/main/images/1681262535006.png)