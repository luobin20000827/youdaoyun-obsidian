---
created: 2024-07-19
updated: 2024-09-05
source: 有道云笔记迁移
tags:
  - 运维
  - Nexus
  - Docker
  - 踩坑
---

## 方法一：用docker拉取nexus镜像
- 遇到的问题
1. Unable to create directory /nexus-data/instance
2. docker-image-container的关系：平台-类-实例
3. 无法使用代理（clash）：开启service_mode-tun模式 https://zhuanlan.zhihu.com/p/153124468
    - 开关代理后需要重启wsl 命令wsl -t Ubantu{此处你的虚拟机名字} -> wsl --shutdown

4. 解决`检测到 localhost 代理配置，但未镜像到 WSL。NAT 模式下的 WSL 不支持 localhost 代理。` 设置.wslconfig `https://www.cnblogs.com/hg479/p/17869109.html`
    - 没法科学上网：打开clash service mode-tun模式

## 方法二：二进制文件进行安装

## 整体迁移，可以搜索网上的迁移方法，是迁移一个文件夹就可以了

## 相关笔记
所属索引：[[Linux-MOC]]
- [[docker常用命令]] — Docker/WSL 操作
- [[基础设施]] — Nexus 仓库规划设计
- [[上传maven仓库时遇到的问题]] — npm 发布踩坑
