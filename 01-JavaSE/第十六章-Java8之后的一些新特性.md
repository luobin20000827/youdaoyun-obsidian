---
created: 2024-04-24
updated: 2024-04-30
source: 有道云笔记迁移
---

# 第十六章-Java8新特性
## 16.1 Java8新特性：Lambda表达式
- Java8 中出现的一些新特性
![image](https://github.com/luobin7/-java-/raw/bbd4d91bc59c57ac62d01c8d6b08b12871ec0072/%E7%AC%AC18%E7%AB%A0_JDK8-17%E6%96%B0%E7%89%B9%E6%80%A7/images/image-20220525201653599.png)

### 16.1.1 语法
- Lambda 表达式：在Java 8 语言中引入的一种新的语法元素和操作符。这个操作符为 “->” ， 该操作符被称为 **Lambda 操作符或箭头操作符**。它将 Lambda 分为两个部分：
-  它只适用于接口，提供了一个更紧凑且方便的方式来实现**只有一个抽象方法**的接口（称为**函数式【接口**，如 Runnable，Callable，Comparator 等）。

- 左侧：指定了 Lambda 表达式需要的参数列表
- 右侧：指定了 Lambda 体，是抽象方法的实现逻辑，也即 Lambda 表达式要执行的功能。

### 16.1.2 范例
- 下面是一些范例：
- 范例一：无参，无返回值：

```
@Test
public void test1(){
    //未使用Lambda表达式
    Runnable r1 = new Runnable() {
        @Override
        public void run() {
            System.out.println("我爱北京天安门");
        }
    };

    r1.run();

    System.out.println("***********************");

    //使用Lambda表达式
    Runnable r2 = () -> {
        System.out.println("我爱北京故宫");
    };

    r2.run();
}
```


- 范例二:一个参数，但没有返回值

```
@Test
public void test2(){
    //未使用Lambda表达式
    Consumer<String> con = new Consumer<String>() {
        @Override
        public void accept(String s) {
            System.out.println(s);
        }
    };
    con.accept("谎言和誓言的区别是什么？");

    System.out.println("*******************");

    //使用Lambda表达式
    //Consumer是java8中为支持lambda功能设计的新接口，代表接受单一输入参数且无返回结果的操作，里面封装的就是accept()方法
    Consumer<String> con1 = (String s) -> {
        System.out.println(s);
    };
    con1.accept("一个是听得人当真了，一个是说的人当真了");

}
```

- 范例三：类型推断

```
@Test
public void test3(){
    //语法格式三使用前
    Consumer<String> con1 = (String s) -> {
        System.out.println(s);
    };
    con1.accept("一个是听得人当真了，一个是说的人当真了");

    System.out.println("*******************");
    //语法格式三使用后
    Consumer<String> con2 = (s) -> {
        System.out.println(s);
    };
    con2.accept("一个是听得人当真了，一个是说的人当真了");

}

Consumer<String> con1 = (String s) -> { System.out.println(s); };  // 显式指定类型
Consumer<String> con2 = (s) -> { System.out.println(s); };  // 类型推断，自动识别为String类型
这两句在功能上是一样的
```
- 范例四：当lambda只需要一个参数时，可以省略参数的小括号

```
@Test
public void test4(){
    //语法格式四使用前
    Consumer<String> con1 = (s) -> {
        System.out.println(s);
    };
    con1.accept("一个是听得人当真了，一个是说的人当真了");

    System.out.println("*******************");
    //语法格式四使用后
    Consumer<String> con2 = s -> {
        System.out.println(s);
    };
    con2.accept("一个是听得人当真了，一个是说的人当真了");
}
```
- 范例五：Lambda 需要两个或以上的参数，多条执行语句，并且可以有返回值

```
@Test
public void test5(){
    //语法格式五使用前
    Comparator<Integer> com1 = new Comparator<Integer>() {
        @Override
        public int compare(Integer o1, Integer o2) {
            System.out.println(o1);
            System.out.println(o2);
            return o1.compareTo(o2);
        }
    };

    System.out.println(com1.compare(12,21));
    System.out.println("*****************************");
    //语法格式五使用后
    Comparator<Integer> com2 = (o1,o2) -> {
        System.out.println(o1);
        System.out.println(o2);
        return o1.compareTo(o2);
    };

    System.out.println(com2.compare(12,6));
}
```
- 范例六：当Lambda 体只有一条语句时，return 与大括号若有，都可以省略

```
@Test
public void test6(){
    //语法格式六使用前
    Comparator<Integer> com1 = (o1,o2) -> {
        return o1.compareTo(o2);
    };

    System.out.println(com1.compare(12,6));

    System.out.println("*****************************");
    //语法格式六使用后
    Comparator<Integer> com2 = (o1,o2) -> o1.compareTo(o2);

    System.out.println(com2.compare(12,21));

}

@Test
public void test7(){
    //语法格式六使用前
    Consumer<String> con1 = s -> {
        System.out.println(s);
    };
    con1.accept("一个是听得人当真了，一个是说的人当真了");

    System.out.println("*****************************");
    //语法格式六使用后
    Consumer<String> con2 = s -> System.out.println(s);

    con2.accept("一个是听得人当真了，一个是说的人当真了");

}
```

### 16.1.3 lambda表达式的本质
- lambda的本质就是在java万事万物皆对象的基础上引入一个更“函数”的编程范式，让他能通过接口实现类来快速实现功能


###  16.2 Java8新特性：函数式(Functional)接口

- 书接上文，Lambda表达式常常与函数式接口一起使用
- 只包含一个抽象方法（Single Abstract Method，简称SAM）的接口，称为函数式接口。当然该接口可以包含其他非抽象方法。
- 我们可以在一个接口上使用 @FunctionalInterface 注解，这样做可以检查它是否是一个函数式接口。同时 javadoc 也会包含一条声明，说明这个接口是一个函数式接口
- 在`java.util.function`包下

- 四个基本的函数式接口

| 函数式接口 | 称谓 | 参数类型 | 用途 |
| --- | --- | --- | --- |
| Consumer<T>  | 消费型接口 | T | 对类型为T的对象应用操作，包含方法： void accept(T t)   |
| Supplier<T>   | 供给型接口 | 无 |返回类型为T的对象，包含方法：T get()   |
| Function<T, R>   | 函数型接口 |T  | 对类型为T的对象应用操作，并返回结果。结果是R类型的对象。包含方法：R apply(T t)   |
| Predicate<T>   | 判断型接口 |  T|  确定类型为T的对象是否满足某约束，并返回 boolean 值。包含方法：boolean test(T t)  |

- 范例：

```
	@Test
	public void test1(){
		List<String> list = Arrays.asList("hello","java","lambda","atguigu");
		list.forEach(s -> System.out.println(s));
    }
// public default void forEach(Consumer<? super T> action)
该方法接受一个Consumer的函数接口（即在方法的参数列表中再接受一个方法），表示接受一个参数但不返回结果的操作
遍历Collection集合的每个元素，执行“xxx消费型”操作。
    
    
	@Test
	public void test2(){
		HashMap<Integer,String> map = new HashMap<>();
		map.put(1, "hello");
		map.put(2, "java");
		map.put(3, "lambda");
		map.put(4, "atguigu");
		map.forEach((k,v) -> System.out.println(k+"->"+v));
// public default void forEach(BiConsumer<? super K,? super V> action)遍历Map集合的每对映射关系，执行“xxx消费型”操作。
	}
```
- 这里比我们上一节练习的lambda表达式更进一步：
    - 上一节中，我们创建了函数式接口的实例，然后调用方法来完成操作
    - 而在这一届中我们直接用lambda在方法调用中插入行为

## 16.3 方法引用与构造器引用
- 方法引用和构造器引用的目的都是为了简化Lambda表达式

### 16.3.1 方法引用格式
- 格式：使用方法引用操作符 `“::”` 将类(或对象) 与 方法名分隔开来。
    - 中间不能有空格，而且必须英文状态下半角输入
- 主要以下三种使用情况：
    - 实例方法引用：对象::实例方法名（不带括号）
    - 静态方法引用：类::静态方法名
    - 对象方法引用：类::实例方法名
    
### 16.3.2 方法引用的一些要求
- 要求一：Lambda体只有一句语句，并且是通过调用一个对象的/类现有的方法来完成的
- 要求二：
    - 针对`实例方法引用`的情况：函数式接口中的抽象方法a在被重写时使用了某一个对象的方法b。如果方法a的形参列表、返回值类型与方法b的形参列表、返回值类型都相同，则我们可以使用方法b实现对方法a的重写、替换。
    - 针对`静态方法引用`：函数式接口中的抽象方法a在被重写时使用了某一个类的静态方法b。如果方法a的形参列表、返回值类型与方法b的形参列表、返回值类型都相同，则我们可以使用方法b实现对方法a的重写、替换。
    - 针对`对象方法引用`：函数式接口中的抽象方法a在被重写时使用了某一个对象的方法b。如果方法a的返回值类型与方法b的返回值类型相同，同时方法a的形参列表中有n个参数，方法b的形参列表有n-1个参数，**且方法a的第1个参数作为方法b的调用者，且方法a的后n-1参数与方法b的n-1参数匹配**（类型相同或满足多态场景也可以）

### 16.3.3 构造器引用
- 当Lambda表达式是创建一个对象，并且满足Lambda表达式形参，正好是给创建这个对象的构造器的实参列表，就可以使用构造器引用。
- 格式：`类名::new`

## 16.4 Stream API
### 16.4.1 什么是Stream API
- 他提供了一些对集合进行操作的工具，方便我们执行复杂的查找，过滤和映射
- 与Collections的区别：Stream面向的是计算，通过CPU实现，而Collections面向的是存储，在内存中实现
- Stream的特点：
    - Stream不会自己存储元素
    - Stream不会改变源对象，而回返回一个新的Stream
    - Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行。即一旦执行终止操作（类似foreach、count、collect），就执行中间操作链（filter、map），并产生结果
    - Stream一旦执行了终止操作，就不能再调用其它中间操作或终止操作了

### 16.4.2 Stream操作的三个步骤
- 创建-中间操作-终止操作
![image](https://github.com/luobin7/-java-/raw/bbd4d91bc59c57ac62d01c8d6b08b12871ec0072/%E7%AC%AC18%E7%AB%A0_JDK8-17%E6%96%B0%E7%89%B9%E6%80%A7/images/image-20220514180803311.png)
#### 16.4.2.1 创建Stream实例
1.通过Collections接口提供的方法
- `default Stream stream() :` 返回一个顺序流

- `default Stream parallelStream() :` 返回一个并行流

```
@Test
public void test01(){
    List<Integer> list = Arrays.asList(1,2,3,4,5);

    //JDK1.8中，Collection系列集合增加了方法
    Stream<Integer> stream = list.stream();
}
```

2.通过Arrays数组提供的静态方法stream()获取数组流
- `static Stream stream(T[] array)`: 返回一个流
    - `public static IntStream stream(int[] array)`
    - `public static LongStream stream(long[] array)`
    - `public static DoubleStream stream(double[] array)`

3.通过Stream的of()
- 使用Stream的静态方法：public static Stream of(T values)返回一个流

```
    @Test
    public void test3(){
        Integer[] arr1 = {1,2,34,5};
        Integer[] arr2 = {7,8,9,10};
        Stream<Integer[]>  stream = Stream.of(arr1, arr2);
        stream.forEach(e->System.out.println(Arrays.toString(e)));
    }
```




4.静态方法iterate()和generate()创建无限流
- 可以使用静态方法 Stream.iterate() 和 Stream.generate(), 创建无限流，但为了避免无限循环，我们要在后续操作种加入`limit(n)`限制个数

    - 迭代 `public static Stream iterate(final T seed, final UnaryOperator f)`

    - 生成 public static Stream generate(Supplier s)


```
    @Test
    public void test4(){
        Stream<Double> s = Stream.iterate(0.5,x -> x*x);
        s.limit(10).forEach(System.out::println);
        Stream<Double> stream1 = Stream.generate(()->Math.random());
        stream1.limit(10).forEach(System.out::println);
    }
```

#### 16.4.2.2 中间操作
1. 筛选和切片

| 方法 | 描述 |
| --- | --- |
| filter(Predicatep) | 接受Predicatep函数表达式（T boolean） |
| distinct() | 通过流所生成元素的 hashCode() 和 equals() 去重操作 |
| limit(long maxSize) | 截断，元素不超过给定个数 |
| skip(long n) | 跳过前n个元素，若不足n个则返回空stream |

2. 映射

| 方法 | 描述 |
| --- | --- |
| map(Function f) | 就是一般的map，`Function<T, R>`表示有输入参与输出的函数表达式 |
| mapToDouble(ToDoubleFunction f) | 同上  |
| mapToInt(ToIntFunction f) | 同上 |
| mapToLong(ToLongFunction f) | 同上 |
| flatMap(Function f) | 接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流 |


```
// 针对map
List<String> words = Arrays.asList("Java", "Stream", "API", "Demo");
List<Integer> wordLengths = words.stream()
    .map(String::length)
    .collect(Collectors.toList());
```

```
// 针对flatMap，这里就是把list中的每个单词都重新打成一个由字母组成的流，再给他拼接成一个大流(HelloWorld),然后去重再输出
List<String> list = Arrays.asList("Hello", "World");
list.stream()
    .flatMap(word -> Arrays.stream(word.split("")))
    .distinct()
    .forEach(System.out::println);
```




3. 排序

| 方法 | 描述 |
| --- | --- |
| sorted() | 返回按自然排序的新stream |
| sorted(Comparator com) | 返回其中按比较器顺序排序 |

#### 16.4.2.3 终止操作
- 流一旦进行终止操作就不可用了
- 终端操作的结果可以是任何不为流的值
1.匹配与查找

| 方法 | 描述 |
| --- | --- |
| allMatch/anyMatch/noneMatch(Predicate p) | 全/至少一个/无匹配 |
| findFirst/Any() | 返回第一个/任意一个元素,他这里都会以Optinal的形式返回，这是一种允许null的容器对象，可以用get取出它的值 |
| count() | 计数 |
| max/min(Comparator c) | 返回流中的最大/最小值 |
| forEach(Consumer c) | 内部迭代 |

```
//find系列操作
Optional<String> optional = list.stream()
    .flatMap(word -> Arrays.stream(word.split("")))
    .distinct()
    .findAny();

if (optional.isPresent()) {
  System.out.println(optional.get());
} else {
  System.out.println("No value found!");
}
```

2.收档
- map与reduce的连接常被成为map-

| 方法 | 描述 |
| --- | --- |
Optional<T> reduce(BinaryOperator<T> accumulator);| BinaryOperator<T> accumulator继承于 BiFunction, 实现 BiFunction.apply(param1,param2) 接口
 | T reduce(T identity, BinaryOperator<T> accumulator); |新增一个初始值identy T，因为有T的存在，因此返回值不是Optional
 
 
```
List<Integer> list=Lists.newArrayList(1,2,3,4,5);
list.stream().reduce((result,item)->{
	System.out.println("result="+result+", item="+item);
	return result+item;
});
		
/* 结果如下：
result=1, item=2
result=3, item=3
result=6, item=4
result=10, item=5
*/

```


3. 收集

| 方法 | 描述 |
| --- | --- |
| collect(Collector c) | 将流转换为其他形式。接收一个 Collector接口的实现，用于给Stream中元素做汇总的方法 |

Collectors提供了很多静态方法，我们可以直接调用的
- Collectors.toList()：把流中元素收集到List
- Collectors.toSet()
- Collectors.toCollection
- Collectors.toMap(Function.identity(), String::length)//Function.identity()是一个恒等函数，所以对列表中的每一个元素，它都返回元素本身。
- Collectors.counting()：计算元素个数
- Collectors.joining()连接

## 16.5 新的语法结构
### 16.5.1 Jshell命令(JDK9)
- 提供了无需创建类，在命令行里直接声明变量进行运算的能力

### 16.5.2 对try-catch结构中流自动关闭的优化(DJDK7)
- 在try的后面可以增加一个()，在括号中可以声明流对象并初始化。try中的代码执行完毕，会自动把流对象释放，就不用写finally了。

```
try(资源对象的声明和初始化){
    业务逻辑代码,可能会产生异常
}catch(异常类型1 e){
    处理异常代码
}catch(异常类型2 e){
    处理异常代码
}
```


### 16.5.3 局部变量类型推断（JDK10）
- 新增了var类型，使得我们不必在声明局部变量的时候显式指定其类型。

```
var list = new ArrayList<String>();  // 推断为 ArrayList<String>
var stream = list.stream();   // 推断为 Stream<String>
```

### 16.5.4 instanceof模式匹配（JDK14）
- 在判断instanceof后可以直接当作该类型使用
```
//旧写法
Object obj = "Hello, world!";

if (obj instanceof String) {
    // 可以直接使用 s，无需进行类型转换
    s = (String) obj
    System.out.println(s.toLowerCase());
} else {
    System.out.println("Not a string");
}
```

```
//新写法
Object obj = "Hello, world!";

if (obj instanceof String s) {
    // 可以直接使用 s，无需进行类型转换
    System.out.println(s.toLowerCase());
} else {
    System.out.println("Not a string");
}
```

### 16.5.5 Switch-case改进（JDK12后的几个版本）
- 引入`yield`替代`return`用于跳出switch语句块
- 改善了case穿透（如果不写`break`，判断一旦成立，后面的匹配都会执行），使用`case L ->`来替代以前的`break`;

```
// 箭头操作符
public class SwitchTest2 {
    public static void main(String[] args) {
        Fruit fruit = Fruit.GRAPE;
        int numberOfLetters = switch(fruit){
            case PEAR -> 4;
            case APPLE,MANGO,GRAPE -> 5;
            case ORANGE,PAPAYA -> 6;
            default -> throw new IllegalStateException("No Such Fruit:" + fruit);
        };
        System.out.println(numberOfLetters);
    }
}
```

- 模式匹配


### 16.5.5 文本快
- 使用`"""`作为文本块的开始与结束符，可以包裹多行文本而不用进行转义


## 16.6 API的变化
## 16.6.1 Option类的引入
- Java8中引入Option来避免空指针异常，Optional<T>既可以存放T的值，也可以存放null，表示这个值不存在。
- 我们可以用isPresent来检测Optional值是否为null，非空则用get()方法返回对象
- 创建Optional对象：
    - static Optional empty()：创建一个空实例
    - static Optional of(T value)：创建一个非空实例
- 获取对象：
    - T get()
    - T orElse(T other) 若空则返回other这个备胎值
    - T orElseGet(Supplier<? extends T> other) ：如果Optional容器中非空，就返回所包装值，如果为空，就用Supplier接口的Lambda表达式提供的值代替

## 16.6.1 String中的新方法
- boolean isBlank()
- String strip()：去除首尾空格
- String stripTriling():去除尾部空格
- String stripLeading()
- String repeat(int n):一个字符串复制n遍 Java -> JavaJavaJava
- int lines().count() 行数统计