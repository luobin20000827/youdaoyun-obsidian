---
created: 2024-05-06
updated: 2024-05-24
source: 有道云笔记迁移
tags:
  - 学习笔记
  - JavaWeb
---

# 第一章 什么是JDBC


## 1.1 JDBC技术概述
- Java Database Connectivity：Java连接数据库技术
- Java连接数据库的一套API，包含一系列类与对应的方法。用于是连接Java程序与数据库软件
- Java只提供规范与接口，数据库厂商则根据接口完成具体的驱动jar包
- 面向接口编程

## 1.2 核心API和使用路线

- 核心类：
    - DriverManager:加载jar包
    - Connection:建立连接
    - Statement 、 PreparedStatement 、 CallableStatement:发送SQL语句
    - Result:获取结果以解析

- 主要的使用路线：`DriverManager-Connection-PreparedStatement-Result`：含动态值语句的SQL

- 复习一下数据库的层次结构：Server-Database-Table

# 第二章 核心API

## 2.1 导入jdbc驱动jar
- 此处使用了postgresql作为例子
- 导入依赖步骤：
    - 创建lib文件夹
    - 导入驱动依赖jar
    - jar右键添加项目依赖

## 2.2 JDBC基本使用步骤
- 注册驱动。
- 获取连接。
- 创建发送 SQL 语句对象。
- 发送 SQL 语句，并获取返回结果。
- 结果集解析。
- 资源关闭。

### 2.2.1 注册驱动

- 在规定连接的url  `String url = "jdbc:postgresql://localhost:5432/JDBC_Test";`，user `String user = "postgres";`以及password `String password = "zsy13913549304";`后，我们就可以用Drivermanager类来完成注册的驱动
    - "注册"一个驱动其实就是让 JDBC 知道在建立数据库连接时应该使用哪个驱动。
- 注册驱动通常有两种办法：
1. 使用Drivermanager直接注册驱动，此时Driver类对象在创建过程中会自动被注册到DriverManger类中；但这么做会存在驱动重复注册的问题

```
DriverManager.registerDriver(new org.postgresql.Driver());
//接下来完成连接，下文不再重复
Connection conn = DriverManager.getConnection(url, user, password);
```

2. 使用反射触发类的初始化完成自动的注册，在Driver类的静态代码块中被注册的，这也就意味着Driver类一加载，Driver类对象就被注册到DriverManager中了


```
Class.forName("org.postgresql.Driver");
```

3. 在java6后的版本中，驱动注册这一步都可以被省略，直接使用url，password和user获取与数据库的连接

### 2.2.2 获取连接

- 使用DriverManager.getConnection()返回连接对象
`Connection conn = DriverManager.getConnection(url, user, password);`
    - 此处的getConnection()也可以接受url和properties两个参数

### 2.2.3 创建statement

- 使用连接对象的createStatement()方法返回statement对象

```
Statement statement = conn.createStatement();
```


### 2.2.4 执行sql

- 发送sql语句并返回ResultSet实例结果
```
String sql = "select * from t_user;";
ResultSet resultSet = statement.executeQuery(sql);
```
- 结合SQL语句五大分类：DDL(Create,Alter,Drop:操作表结构),DML(Insert,Update,Delete:增删改),DQL(Query:查询),DCL(Date Control:权限控制),TCL(Transcation Control:事务控制)
    - 对于DQL，我们一般使用`ResultSet executeQuery(sql)`，返回结果封装对象ResultSet
    - 而对于非DQL(DML,DDL)，我们一般使用`Int executeUpdate(sql)`，他会返回一个int值，表示收到影响的数据调试
    - DCL，一般不在Java代码中进行，一般在数据库层面
    - TCL使用Connection 的`setAutoCommit(false)，commit()和rollback()`方法来进行的。

### 2.2.5 进行结果集解析
- 此处返回的ResultSet并不属于collection框架，而是 JDBC规范的一部分
- 它可以理解为一个表格，行的滚动由next方法进行控制，而列的访问使用Result.getXxx("字段名"or索引值)
- 此处的next也并不是Iterator()接口中的内容，只是同名且用法类似
- 以上也可以根据columnIndex和columnLabel获取，注意角标都是从1开始的

```
while (resultSet.next())
        {
            int id = resultSet.getInt("id");
            String account = resultSet.getString("account");
            String password = resultSet.getString("password");
            String nickname = resultSet.getString("nickname");

            System.out.println(id + "--" + account + "--" + password + "--" + nickname);
        }
```

- ResultSet返回的是表中的值，而如果要获取有关列的对象，我们应该使用ResultSetMetaData，例如列名就可以使用String getColumnLable(int 列index)




## 2.3 基于statement方式存在的问题

- 注入攻击
- sql语句形式相当单一，只能接受字符串
- 综上，statement只适用于静态的，不包含动态值的数据库操作
- 因此我们引入了最常用的PreparedStatement预编译

## 2.4 PreparedStatement预编译
- 常规的Statement：
    - 创建Statement
    - 拼接SQL
    - 发送SQL，解析结果
- 预编译PreparedStatement
    - 编写SQL并用占位符?代替动态值
    - 创建 PreparedStatement, 并且传入动态值
    - 使用setxxx(占位符index，值)给占位符位置赋值即可
    - 发送并解析结果

```
      // 1. 编写 SQL 语句结果
        String sql = "select * from t_user where account = ? and password = ? ;";

        // 2. 创建预编译 Statement 并且设置 SQL 语句结果
        PreparedStatement preparedStatement = connection.prepareStatement(sql);

        // 3. 单独的占位符进行赋值
        /*
        参数 1: index 占位符的位置从左向右数从 1 开始, 账号 ? 1
        参数 2: object 占位符的值可以设置任何类型的数据，避免了我们拼接且类型更加丰富
         */
        preparedStatement.setObject(1, account);
        preparedStatement.setObject(2, password);

        // 4. 发送 SQL 语句, 并获取返回结果
        /*
        statement.executeUpdate 或 executeQuery (String sql);
        preparedStatement.executeUpdate 或 executeQuery();    TODO: 因为它已经知道语句，知道语句动态值
         */
        ResultSet resultSet = preparedStatement.executeQuery();
```
![image](https://camo.githubusercontent.com/91e7d11020a853e99ee8ccfa3453f8c7b7e979609cfacf140d65c0eebe0b4d1f/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f66643539323864653661353634303433386333343861323235636466356634392e706e67)

# 第三章 优化与扩展

## 3.1 主键回显
- 针对含自增长主键的表，我们在插入后需要拿到对应主键的值

```
PreparedStatement preparedStatement = connection.prepareStatement(sql, PreparedStatement.RETURN_GENERATED_KEYS);//在构造PreparedStatement时加入PreparedStatement.RETURN_GENERATED_KEYS参数
ResultSet generatedKeys = pstatement.getGeneratedKeys();
            for (int n : ns) {
                System.out.println(n + " inserted."); // batch中每个SQL执行的结果数量
            }

            while(generatedKeys.next()){
                int id = generatedKeys.getInt(1);
                System.out.println("id = " + id);
            }   // 移动下光标
```


## 3.2 Bactch批量操作
- 当我们要操作大量SQL语句，特别是哪些占位符位置相同只是取值不同的SQL而言，将每个PreparedStatement单独执行的效率很低
- 此时我们可以将若干条只有参数不同的语句所谓batch执行，这种操作有特殊优化可以使苏大大幅提升

```
try (PreparedStatement ps = conn.prepareStatement("INSERT INTO students (name, gender, grade, score) VALUES (?, ?, ?, ?)")) {
    // 对同一个PreparedStatement反复设置参数并调用addBatch():
    for (Student s : students) {
        ps.setString(1, s.name);
        ps.setBoolean(2, s.gender);
        ps.setInt(3, s.grade);
        ps.setInt(4, s.score);
        ps.addBatch(); // 添加到batch
    }
    // 执行batch:
    int[] ns = ps.executeBatch();
    for (int n : ns) {
        System.out.println(n + " inserted."); // batch中每个SQL执行的结果数量
    }
}
```
- 主要差别：pStatement.addBatch()和int pStatement.executeBatch()(每一个batch影响的结果数量)

## 3.3 事务操作TCL
- 事务特性：ACID
- 超过两条组合的sql应该都使用事务
- 通过事务自动提交关闭开启控制 

```
Connection conn = openConnection();
try {
    // 关闭自动提交:
    conn.setAutoCommit(false);
    // 执行多条SQL语句:
    insert(); update(); delete();
    // 提交事务:
    conn.commit();
} catch (SQLException e) {
    // 回滚事务:
    conn.rollback();
} finally {
    conn.setAutoCommit(true);
    conn.close();
}
```

## 3.4 连接池
- 由于创建于销毁JDBC连接开销比较大，类似于线程池的思想，Java中引入了连接池来复用已经创建好的连接
- 常用的连接池有：
    - HikariCP
    - C3P0
    - BoneCP
    - Druid
- 这些连接池都遵守`javax.sql.DataSource`规范
- 此处我们以国产阿里连接池Druids为例

### 3.4.1 创建连接池
- 硬编码：

```
DruidDataSource druidDataSource = new DruidDataSource();

        // 设置参数
        // 必须参数: 连接数据库驱动类的全限定符, 注册驱动(url 或 user 或 password)
        druidDataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");    // 帮助我们进行驱动注册和获取连接
        druidDataSource.setUrl("jdbc:mysql://localhost:3306/my_jdbc");
        druidDataSource.setUsername("MYXH");
        druidDataSource.setPassword("520.ILY!");

        // 非必须参数: 初始化连接数量, 最大的连接数量...
        druidDataSource.setInitialSize(5);    // 初始化的连接数量
        druidDataSource.setMaxActive(10);    // 最大的连接数量

        // 获取连接
        Connection connection = druidDataSource.getConnection();

        // 数据库 CURD

        // 回收连接
        connection.close();    // 连接池提供的连接, close()就是回收连接
```

- 实际使用中，我们一般使用软编码的形式创建连接池，即将配置存储在外部文件中。
- 配置信息存放在src/druid.properties

```
读取配置信息时，我们可以使用properties.load(fis)的方式，蛋更多使用的还是用类加载器提供的方法
InputStream is = 类名.class.getClassLoader().getResourceAsStream("配置文件名");//此处在当前类的classpath下中的所有文件中查找配置文件，因此要求配置文件在src目录（一般来说）下，并返回一个inputStream对象
InputStream is = ClassLoader.getSystemClassLoader().getResourceAsStream("config.properties");
也可以走系统类加载器加载这个文件
下面的conn流程都一致
```

# 第四章 DAO

## 4.1 DAO的概念
- 每一张表都会写一个DAO接口完成对该表增删改查的操作
- 具体要怎样操作DAO放在service实现

## 4.2 BaseDAO的设计理念
- DML操作就设计一个executeUpdate()，这个就常规设计就可以
    - 有一个可变参数Object... args可以放在方法的参数列表中
- DQL操作查询executeQuery()的设计比较特别
    - 由于设计时不知道数据类型，需要从传入侧获得，因此我们需要一个泛型参数 Class<T> clazz来获取数据类型，返回值则设计为<T> List<T>
    - 最后的结果中需要用反射与metadata来将数据库返回的数据转化为我们的实体类（行（for循环）-列（getDeclaredXXX）-解析出数据填充进我们反射拿到的newInstance）

## 相关笔记
所属索引：[[JavaWeb-MOC]]
- [[MyBatis]] — JDBC 的框架化封装
- [[Maven]] — 驱动依赖管理
