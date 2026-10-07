---
created: 2024-03-27
updated: 2024-04-03
source: 有道云笔记迁移
tags:
  - 学习笔记
  - Java
---

# 第九章-常用类和基本API

## 9.1 String类

### 9.1.3 String构造
1. 所有String字符串对象都是java.lang.String类的实例
    - final 不可继承
    - Serializable 可序列化，可以转化为流数据进行IO操
    - Comparable 可比较
2. String中对对象的存储时通过其内部的`private final char value[]`实现的，也是是说abc其实也就等效于`char[] value = ['a','b','c']`
3. 特别的，在jdk9之后，String引入了Compact String优化，动态选择使用一个字节（byte[]）还是两个字节（等效于之前的char[]）来存储每个字符

![image](https://github.com/luobin7/-java-/raw/main/%E7%AC%AC11%E7%AB%A0_%E5%B8%B8%E7%94%A8%E7%B1%BB%E5%92%8C%E5%9F%BA%E7%A1%80API/images/image-20220514184404024.png)

4. 为什么String不用new()构造：字符串常量池
- 字符串常量都存放在字符串常量池（StringTable）中
- 池中没有两个重复的字符串
- 从JDK7开始，常量池从方法区（永久代）移到堆空间中---出于回收的考虑
- 虽然存在向量痴，但new()构造的String和向量池中的同名变量是不相等的
    - （1）只有常量+常量或是intern()返回的结果才会在常量池中，其他都会通过new的方式创建一个新的字符串，存放在堆空间中
    - concat方法拼接，哪怕是两个常量对象拼接，结果也是在堆。
    - `String s = new String("ABC");`这样的写法创建了两个字符串，一个在常量池里面，一个在堆内存里。

### 9.1.4 String的常用API
1. 构造器
- 字面量定义：`String abc = 'abc'`
- 构造器定义:`String abc = new String('abc')`
- 通过字符数组构造：`char[] abc = {'A','B','C'}; String ABC = new String(abc,0,1) // String ABC = 'A'`
- 通过字符数组构造：`byte bytes[] = {97, 98, 99 }; String str5 = new String(bytes); String str6 = new String(bytes,"GBK");`

2. 转换方法
- 总原则，转到哪个类型就用哪个类型的方法
    - String转基本数据类型，类似的`public static int parseInt(String s)：Integer.parseInt('123')`,其他数据类型同理
    - 其他基本数据类型转String，例如对应int的public static String valueOf(int n) //此外包装类还内置了toString方法，但要注意只能是包装类使用
    - String与字符数组的转换：String--> char[]使用`public char[] toCharArray()`和`public void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin)`; char[] --> String；使用String使用字符数组的构造器
    - String与字节数组byte[]的转换：
    String --> byte[]，使用String 的构造方法 `String s1 = new String(new byte[]{'a','b'})` ; byte[] --> String，使用`public byte[] getBytes()`方法.
- 对于中文字符 GBK中一个中文字符占两个字节，而UTF-8种占3个字节
- 一些String常用的方法：
    - public char charAt(int index): 返回指定索引处的字符。索引范围为从零到“字符串长度 - 1”。
    - public int length(): 返回此字符串的长度。长度等于该字符串中的字符数。
    - public boolean isEmpty(): 返回此字符串的长度是否为零。
    - public String toLowerCase(): 将该字符串中的所有字符都转换为小写。
    - public String toUpperCase(): 将该字符串中的所有字符都转换为大写。
    - public boolean equals(Object anObject): 将此字符串与指定的对象比较。如果该对象是一个String，那么这个函数会逐个比较两个字符串中的字符，是否完全相同。
    - public boolean equalsIgnoreCase(String anotherString): 将此String与另一个String进行比较，忽略大小写。
    - public String substring(int beginIndex, int endIndex): 返回一个新字符串，它是此字符串的一个子字符串，具有原始字符串中指定的索引之间的字符（左闭右开）。
    - public String[] split(String regex): 根据给定的正则表达式的匹配拆分此字符串。
    - public String replace(CharSequence target, CharSequence replacement): 使用指定的字面值替换序列替换此字符串的全部匹配的字面值目标序列。
    - public int indexOf(int ch): 当此字符第一次出现在该字符串中时返回索引值。
    - public int indexOf(String str): 返回指定子字符串在此字符串中第一次出现的索引值。
    - public int lastIndexOf(int ch): 返回此字符最后一次出现在该字符串中的索引。
    - public int lastIndexOf(String str): 返回指定子字符串在此字符串中最右边出现处的索引。
    - public boolean contains(CharSequence s): 当且仅当此字符串包含指定的char值序列时返回true。
    - public booleand startsWith/endsWith 测试此字符串是否以指定的前缀开始

### 9.1.5 StringBuffer和StringBuilder
- 为了解决String不可变的问题，java.lang包提供了可变字符序列StringBuffer和StringBuilder类型。
- 区分String、StringBuffer、StringBuilder
    - String:不可变的字符序列； 底层使用char[]数组存储(JDK8.0中)
    - StringBuffer:可变的字符序列；线程安全（方法有synchronized修饰），效率低；底层使用char[]数组存储 (JDK8.0中)
    - StringBuilder:可变的字符序列； jdk1.5引入，线程不安全的，效率高；底层使用char[]数组存储(JDK8.0中)
- 一些常用的api
    - 增删改查（append,delete,replace/setCharAt/insert/reverse,charAt）
    - 校验字串转化api（indexOf,lastIndexOf,subString,toString）
    - 长度相关的api（length(),setLength()(短则截断，长则填充null)）

## 9.2 时间API
### 9.2.1 java.lang.System内的方法
- public static currentTimeMillis() 返回当前时间与中央标准时间（1970.1.1）之间以毫秒为单位的时间差

### 9.2.2 java.util.Date类内的方法
- 构造器
    - Date()
    - Date(long毫秒数)：把该毫秒值换算成日期时间对象
- 常用方法
    - getTime(): 返回自 1970 年 1 月 1 日 00:00:00 GMT 以来此 Date 对象表示的毫秒数。
    - toString(): 把此 Date 对象转换为以下形式的 String： dow mon dd hh:mm:ss zzz yyyy 其中： dow 是一周中的某一天 (Sun, Mon, Tue, Wed, Thu, Fri, Sat)，zzz是时间标准。

### 9.2.3 java.text.SimpleDateFormat 日期时间的解析和格式化
- 使用sdf对象设置的pattern对传进来的Date对象进行格式化format()为符合目标格式的String或者解析parse()为Date对象
- 详细可以见文档中提供的一些sample和具体每个字符含义的说明

### 9.2.4 java.util.Calendar(日历)
- JDK1.1之后用于改进Date

```
public static void main(String[] args) {
        Calendar c = Calendar.getInstance();
        char[] chars = {'日', '一', '二', '三', '四', '五', '六'};

        // 把时间调到这个月的第一天
        c.set(Calendar.DAY_OF_MONTH, 1);
        int dow = c.get(Calendar.DAY_OF_WEEK);

        // 打印星期标题
        for (char ch : chars) {
            System.out.printf("%3c", ch);
        }
        System.out.println();

        // 根据星期几进行空格填充
        for (int i = 1; i < dow; i++) {
            System.out.print("   ");
        }

        // 打印该月所有天数，这里先简单假设每月30天
        for (int i = 1; i <= c.getActualMaximum(Calendar.DAY_OF_MONTH); i++) {
            System.out.printf("%3d", i);

            // 当打印到每周最后一天需要换行
            if ((i + dow - 1) % 7 == 0) {
                System.out.println();
            }
        }
    }
```
### 9.2.5 JDK8后新的日期时间api
- 现在一般使用java8中引入的java.time API
- 包中的所有类都是不可变和线程安全的
- 本地日期时间：LocalDate、LocalTime、LocalDateTime
- 瞬时：Instant
- 格式化：DateTimeFormatter
- 其他：时区、与老格式的转换、持续时间等等

## 9.3 比较器
- 引用数据类型不能直接比较大小，因此引入比较器
- 对象数组的排序问题-设计对象之间的比较问题
    - 自然排序java.lang.Comparable
    - 定制排序java.util.Comparator
    - 
### 9.3.1 通过Comparable接口实现排序

- 实现Comparable接口
- 重写接口中的compareTo(object o)中比较大小的方法
- 创建多个实例，进行比较或者排序

```
package java.lang;

public interface Comparable{
    int compareTo(Object obj);
}
```
- 实现Comparable接口的对象列表（和数组）可以通过 Collections.sort 或 Arrays.sort进行自动排序。实现此接口的对象可以用作有序映射中的键或有序集合中的元素，无需指定比较器。

### 9.3.2 定制排序
- 用在不方便直接修改源代码的情况
- 通过制定一个专用的比较器类，并在比较器类中实现Comparator接口
- 重写compare(object o1,object o2)方法
- 传入Arrays.sort(待比较数组，Comparator对象) 等方法中比较
- 
```
package com.atguigu.api;

import java.util.Comparator;
//定义定制比较器类
public class StudentScoreComparator implements Comparator { 
    @Override
    public int compare(Object o1, Object o2) {
        Student s1 = (Student) o1;
        Student s2 = (Student) o2;
        int result = s1.getScore() - s2.getScore();
        return result != 0 ? result : s1.getId() - s2.getId();
    }
}

@Test
public void test01() {
    Student[] students = new Student[5];
    students[0] = new Student(3, "张三", 90, 23);
    students[1] = new Student(1, "熊大", 100, 22);
    students[2] = new Student(5, "王五", 75, 25);
    students[3] = new Student(4, "李四", 85, 24);
    students[4] = new Student(2, "熊二", 85, 18);

    System.out.println(Arrays.toString(students));
    //定制排序
    Arrays.sort(students, new StudentScoreComparator());
}
```
### 9.4 其他的一些API
- java.lang.System类：IO流
- java.lang.Runtime类：单例，可以查看内存占用等运行时的状态
- java.lang.Math类：静态
    - BigInteger类 取代Integer用作大整数运算
    - BigDecimal，取代double和float表示任意精度的浮点数
- java.util.Random：产生随机数


## 相关笔记
所属索引：[[JavaSE-MOC]]
