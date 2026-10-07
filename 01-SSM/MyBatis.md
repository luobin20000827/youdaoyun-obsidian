---
created: 2024-06-03
updated: 2024-06-20
source: 有道云笔记迁移
---

## 1. 什么是MyBatis
- 持久化框架，替代JDBC
    - 持久化：让数据从内存到数据库中
- 使用XML和注解配置和映射数据类

### 1.1 特点
- 简单易学,灵活
- 解除耦合,sql和代码的分离，提高了可维护性。
- 提供映射标签，支持对象与数据库的ORM字段关系映射。
- 提供对象关系映射标签，支持对象关系组建维护。
- 提供xml标签，支持编写动态sql。

## 2. 搭建一个MyBatis程序

### 2.1 配置xml
- 在xml中

```
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=utf8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

### 2.2 使用工具类拿到会话SqlSession

- 在工具类中使用工厂模式生成SqlSession
- 最常用的就是从xml中读取配置信息生成
- xml-SqlSessionFactory-SqlSession

```
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.IOException;
import java.io.InputStream;
public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;
    static {
        try {
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    // 设计成代码块的形式可以让整个sqlSessionFactory随着类的初始化而加载完成，这样就可以直接拿到SqlSession
    //获取SqlSession连接
    public static SqlSession getSession(){
        return sqlSessionFactory.openSession();
    }
}
```
### 2.3 Mapper接口文件
- xml配置文件替代了原本dao层的接口实现类，而原本的接口就以xxxMapper命名

```
// Mapper接口类
public interface SysUserMapper {
    List<SysUser> selectUserList();
    int insertUser(SysUser sysUser);
}
```

### 2.4 在mapper-config中配置映射
- mapper.xml和mapper接口之间是靠namespace连接的
- 而mapper.xml和整个mapper-config之间是要后者中的<mapper>标签连接的

### 2.5 mapper.xml中编写sql
- mapper.xml中sql语句中的变量名是数据库表中的字段名，而占位符中的则是你传入的参数
    - 如果指明了parameterType，那占位符就直接与parameterType对应即可，若无parameterType：
        - 在方法只有一个参数名时，占位符的名字可以任意取（MyBatis 会自动将其映射到唯一的参数）
        - 如果有多个参数，则占位符名字需要与方法参数名一致（可以通过 @Param 注解指定参数名）`SysUser selectUserByUsernameAndPassword(@Param("username") String username, @Param("password") String password);`
        - 多种参数的情况也可以用map实现，map中传入entry `占位符:查询值`即可
- CRUD操作必要要提交事务`sqlSession.commit()`
- 对自增id的处理使用useGeneratedKeys和keyProperties 或是selectedKeys（基于connection）

```
<insert id="insertUser" parameterType="com.robin.mybatis.pojo.SysUser" useGeneratedKeys="true" keyProperty="uid">
        INSERT INTO sys_user(username, user_pwd)
        VALUES (#{username}, #{user_pwd})
    </insert>
// 这种用法需要实体类作为入参，不然程序不知道主键值插到哪里（不认识这个uid）
```
- 路径问题：Mapper文件在resource文件下中要按照对应的mapper文件创建
## 3. MyBatis配置优化

### 3.1 mybatis-config
- environment：适配同一套sql映射不同的数据库
    - id：标识不同环境，默认的环境default指定
    - transactionManager：指定事务管理器的类型，通常都使用JDBC
    - datasource：可以在此指定连接池
    - 具体的配置信息可以用properties传递

```
<environments default="development">
  <environment id="development">
    <transactionManager type="JDBC">
      <property name="..." value="..."/>
    </transactionManager>
    <dataSource type="POOLED">
      <property name="driver" value="${driver}"/>
      <property name="url" value="${url}"/>
      <property name="username" value="${username}"/>
      <property name="password" value="${password}"/>
    </dataSource>
  </environment>
</environments>
```

- Mapper标签中可以使用相对路径/class（需要配置文件名称和接口名称一致，并且位于同一目录下）具体的mapper.xml
- typeAliases标签可以简化xml中的类名
- 还有一些setting配置：类似缓存/驼峰命名规则转换/日志实现

```
<!--配置别名,注意顺序-->
<typeAliases>
    <typeAlias type="com.kuang.pojo.User" alias="User"/>
</typeAliases>
// 或者添加下面的<pacakge>包名，这样会自动扫描包内的类并拿其小写名作为别名
<typeAliases>
    <package name="com.kuang.pojo"/>
</typeAliases>
```


### 3.2 ResultMap类型
- 当遇到数据库字段名与pojo类不一致时可以创建一个ResultMap映射来解决（查询语句中的别名也可以）

```
<resultMap id="UserMap" type="User">
    <!-- id为主键 -->
    <id column="id" property="id"/>
    <!-- column是数据库表的列名 , property是对应实体类的属性名 -->
    <result column="name" property="name"/>
    <result column="pwd" property="password"/>
</resultMap>
<select id="selectUserById" resultMap="UserMap">
    select id , name , pwd from user where id = #{id}
</select>
```
- 这只是resultMap使用的一个小例子，我们可以使用它来对结果进行各种映射，包括后面的association和collection标签

### 3.3 日志配置
- 在mapper-config setting中配置
- 配置文件logger.propertis
- 日志级别：info，error

## 4. Mybatis分页

### limit实现
- limit start_page_index page_index
- limit a,-1 -> a+1 ~ last
- limit n = limit(0,n)

### RowsBounds分页实现
- 基于面向对象的分页方法，侵入性较小

```
   RowBounds rowBounds = new RowBounds((currentPage-1)*pageSize,pageSize);
   //通过session.**方法进行传递rowBounds，[此种方式现在已经不推荐使用了]
   List<User> users = session.selectList("com.kuang.mapper.UserMapper.getUserByRowBounds", null, rowBounds);
```

### 基于插件的实现(ex.PageHelper)
- 和rowsbound类似

## 5.MyBatis中的注解开发
- 直接在接口方法中写sql，替代xml配置，类似

```
    @Delete("DELETE FROM sys_user WHERE username = #{name}")
    public Integer DeleteUserByName(@Param("name") String name);
```

- 常见的注解
    - @Insert：实现新增
    - @Update：实现更新
    - @Delete：实现删除
    - @Select：实现查询
    - @Result：实现结果集封装
    - @Results：可以与@Result 一起使用，封装多个结果集
    - @One：实现一对一结果集封装
    - @Many：实现一对多结果集封装
- 文档中不推荐通过注解是实现MyBatis

## 6.MyBatis执行流程
![image](https://img-blog.csdnimg.cn/fb8f06d7d8c544c8a9bf1885c44bf3be.png)



## 7. 多表关联查询
- 模拟sql中的关联查询/子查询/多表连接查询等等
### 7.1 多对一的查询
- resultmap中使用association关联
### 7.1.1 按照查询嵌套处理
- 将column中指定的列`column="tid"`在给定查询方法`select="getTeacher"`中查到的实体返回给对应的property `property="teacher"`

```
<resultMap id="StudentTeacher" type="com.robin.mybatis.pojo.Student">
        <id column="id" property="id"/>
        <result column="name" property="name"/>
        <association property="teacher" column="tid" javaType="com.robin.mybatis.pojo.Teacher" select="getTeacher"/>
    </resultMap>
    <select id="getStudent" resultMap="StudentTeacher">
        select * from Student where id =#{id}
    </select>
    <select id="getTeacher" resultType="com.robin.mybatis.pojo.Teacher">
        select * from Teacher where id = #{id}
    </select>
```

### 7.1.2 按照结果嵌套 
- 拿着sql查出来的属性`<select s.name sname , s.id sid,t.name tname from Student s,Teacher t where s.tid = t.id and s.id = #{id}>`去映射我们需要的类`<association property="teacher" javaType="com.robin.mybatis.pojo.Teacher">`

```
    <resultMap id="StudentTeacher2" type="com.robin.mybatis.pojo.Student">
        <id column="sid" property="id"/>
        <result column="sname" property="name"/>
        <association property="teacher" javaType="com.robin.mybatis.pojo.Teacher">
            <result property="name" column="tname"/>
        </association>
    </resultMap>
    <select id="getStudent2" resultMap="StudentTeacher2">
        select s.name sname , s.id sid,t.name tname from Student s,Teacher t where s.tid = t.id and s.id = #{id}
    </select>

```

### 7.2 一对多的查询
- resultmap中使用collection关联
- 用法与association类似，分按结果嵌套（写一个大sql并根据字段嵌套映射）和按查询嵌套（在映射中再嵌入一个查询）
- 注意当属性为list区分javatype（List）和oftype（<泛型>）

```
//结果嵌套
    <resultMap id="TeacherResultMap" type="com.robin.mybatis.pojo.Teacher">
        <result property="name" column="tname"/>
        <collection property="students" javaType="ArrayList" ofType="com.robin.mybatis.pojo.Student">
            <result property="id" column="tid"/>
            <result property="name" column="sname"/>
        </collection>
    </resultMap>
    <select id="selectTeacherByName" resultType="com.robin.mybatis.pojo.Teacher" parameterType="String" resultMap="TeacherResultMap">
        select t.id tid ,t.name tname,s.id sid,s.name sname from teacher t left join student s on s.tid = t.id where t.name = #{name};
    </select>
```


```
    //查询嵌套
    <resultMap id="TeacherResultMap2" type="com.robin.mybatis.pojo.Teacher">
        <result property="name" column="name"/>
        <collection property="students" javaType="ArrayList" ofType="com.robin.mybatis.pojo.Student" select="getStudent" column="id"/>
    </resultMap>
    <select id="selectTeacherByName2" resultType="com.robin.mybatis.pojo.Teacher" parameterType="String" resultMap="TeacherResultMap2">
        select * from teacher where name = #{name};
    </select>
    <select id="getStudent" parameterType="int" resultType="com.robin.mybatis.pojo.Student">
        select * from student where tid = #{id}
    </select>
```
## 8. 动态sql
- MyBatis提供了三个主要的关键字
    - if
    - choose(when,otherwise) //和if的区别是他是switch，类似与or
    - trim(where set) where和set两者效果基本是一致的，set用在update中
    - foreach 构筑in条件，再sql中遍历集合

- https://blog.csdn.net/qq_42391904/article/details/106108005


```
    <select id="getStudentIn" resultMap="StudentTeacher2" parameterType="map">
        select s.name sname , s.id sid,t.name tname from Student s inner join Teacher t on s.tid = t.id
        <where>
        s.id in
            <foreach collection="ids"  item="id" open="(" close=")" separator=",">
                #{id}
            </foreach>
        </where>
    </select>
```


## 9.MyBatis缓存
- 一级缓存是sqlsession层面的
    - 本质是map
    - 对查询数据表的增删改查会清空当前的缓存区，防止脏读
    - 当session关闭或调取了clearCache()方式时
- 二级缓存时namespace级别的
    - 需要在setting中开启，并在每个mapper中配置使用

```
<setting name="cacheEnabled" value="true"/>
```

```
<cache/>
官方示例=====>查看官方文档
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
这个更高级的配置创建了一个 FIFO 缓存，每隔 60 秒刷新，最多可以存储结果对象或列表的 512 个引用，而且返回的对象被认为是只读的，因此对它们进行修改可能会在不同线程中的调用者产生冲突。
```
