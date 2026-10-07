---
created: 2024-07-20
updated: 2024-09-11
source: 有道云笔记迁移
tags:
  - 运维
  - Docker
  - WSL
---

## 8.26
- iamge-container-volume 镜像-容器-数据卷
    - 对应的就是模板（image），实例（container）还有映射容器内数据的方法（volume）
    - docker network，可以将几个容器防灾一个网络中实现互联

## 8.27
1. wsl2和主机的端口是公用的，像3306这种端口很容易出现冲突
2. 在wsl2中查看，宿主机用来访问wsl2的ip `ifconfig | grep eth0 -n1 | grep inet | awk '{print $3}'`，在宿主机中用`ip addr`也可以查看
3. 在wsl2查看宿主机的ip `ip route show | grep -i default | awk '{ print $3}'`，但是实际使用这个ip是ping不通的在网上找了个解决方法：`打开 wsl ， 输入 cat /etc/resolv.conf ；
复制 nameserver 后面的 ip 地址`
4. 查看网上的说法，一般wsl2实际使用可以开启镜像模式，这样就直接用localhost访问wsl2中搭建的服务
5. netsh用来实现win上端口的转发，支持添加转发、删除和查看转发情况

## 8.29 
1. excel笛卡尔积公式：`https://jingyan.baidu.com/article/219f4bf7ee29f9de452d387a.html`

## 8.30
1. help后端组件更新当时是用了一个sh脚本批量上传的本地jar包
2. 前端npm组件更新时是先传到gitlab，再上yyf的电脑用本地打包上传的

## 9.2 
1. gitlab修改项目克隆地址的前缀需要在`[root@localhost ~]# vim /opt/gitlab/embedded/service/gitlab-rails/config/gitlab.yml
`修改他的yml实现的
2. gitlab docker部署的话一般是需要映射三个端口80，443，20端口
3. 一直要求输密码输的是登录网站的密码：一般都是去gitlab/github上看生成的token

## 9.8 
- wsl2是看靠前的eth来看究竟哪个是wsl主机的ip，而且一般永远可以通过默认的端口映射来在localhost上访问：redmi上对应eth2
- nacos 黑马项目pom里面的配置有问题

## 相关笔记
所属索引：[[Linux-MOC]]
- [[Nexus部署]] — Nexus 容器化部署
