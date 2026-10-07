---
created: 2024-10-16
updated: 2024-10-21
source: 有道云笔记迁移
tags:
  - 学习笔记
  - SpringBoot
---

- 声明式事务（@Trnasaction）和编程式事务（TransactionTemplate，控制事务更精细，自由，例如我可以只控涉及数据库修改代码块的事务）
    - 两者对异常的捕获也是有区别的，声明式事务如果你把异常捕获了而不抛出去，他就不会触发回滚
- 用transactionTemplate：不需要返回值就在里面重写TransactionCallbackWithoutResult
- 

## 相关笔记
所属索引：[[SpringBoot-MOC]]
- [[SpringBoot]] — SpringBoot 笔记
- [[Spirng]] — Spring 基础
