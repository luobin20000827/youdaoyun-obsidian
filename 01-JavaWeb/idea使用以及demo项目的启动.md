---
created: 2024-09-20
updated: 2024-09-20
source: 有道云笔记迁移
---

# 3.IDEA使用

## 3.1 IDEA下载
- 长亮开发使用的IDEA版本必须是2022.2.3 (长亮APStack插件绑定此版本，社区版/终极版不影响)，解压安装版在新核心培训群中下载`开发工具`压缩包获得
- 下载后解压即可，点击`/根目录/bin/idea64.exe`使用
- 注意解压路径请不要带有中文

## 3.2 IDEA配置
- idea里配置好java，maven，详见x.x
- 安装长亮ap-stack插件：
    - 点击左上角`File`，下拉菜单中选择`Settings`，右侧菜单选择`Plugins`
    - 点击右上角`设置小齿轮图标`，下拉选择`Install Plugin From Disk`
    - 选择APStack对应的zip压缩包(`apstack-toolkit-plugin-2.2.8-RELEASE-223.zip`)导入
    - 右下角点击`Apply`后生效
    - 类似插件也可以按照该方式安装
- 注意：插件生效后生效后，APStack按钮会出现在最右侧边框，展开后点击`高级`-`关闭GPU硬件加速`（否则`demo-tran`-`resource`文件夹下服务编排xml中无法显示图形化界面）

# 4.Git配置

## 4.1 本地Git安装
- git安装无特殊要求，下载安装exe，一直点击下一步即可
- 安装完成后右击显示有`Git Bash Here`即安装成功
- 安装之后设定本机用户名，绑定邮箱，让远程服务器知道提交者的身份

```
git config --global user.name "your_name" //此处设置的用户名不影响你在gitlab提交时显示ide用户名
git config --global user.email "your_email@xx.com"
//正常来说，此处设置完就可以以命令行的方式clone远端仓库了
git clone http://xxx.git //项目地址
git commit -m "your_message"
git push
git pull
```


## 4.2 连接远程gitlab代码仓库 （idea内置）
- 当前demo项目自带.git文件（隐藏），已经绑定`10.10.20.150/POC/demo-parent/`
- 若要绑定省联社内网的远端仓库，点击右上角`Git`-`Manage Remote`，点击右上角＋号添加需要绑定的远程仓库url于branch分支名即可（会提示你输入gitlab账号）
- 绑定后即可正常Git操作

# 5.DBeaver使用
- 解压文件夹后，点击`dbeaver.exe`打开软件
- 点击创建连接，数据库类型选择Mysql（按照实际使用的数据库类型选择，此处以demo中使用的mysql为例）
    - 此时若报错找不到合适的驱动，则点击`编辑驱动设置`，将`库`一栏的无效驱动删除，手动添加本地的驱动jar包
- 设置中输入使用的url,端口，用户名和密码，点击`测试连接`显示联通则连接成功
    - 此时若显示`allowPublicKeyRetrieval`等属性相关的错误，则在`驱动属性`一栏中调整这些属性的值

# 6.demo运行
- 将`demo-test`模块下`resource`中`apllication.properties`中的数据库配置改为连接的数据库配置（`datasource`关键字）
    - 这里要注意启动类`DemoAServer`中也可以通过System.setProperty("your_property","your_value")的方式配置启动参数，而且这里的配置优先级高于application.properties中的
- 启动`demo-test`中`DemoAServer`启动类来启动服务
- 对应三个交易的测试的报文格式在`demo-tran`模块下`报文`文件夹查看，复制对应报文内容在postman或`demo-trans`模块下`resource`文件夹下服务编排对应xml中的接口测试模块中发起请求测试。
    - 或使用群里的postman请求模板

