---
created: 2024-04-12
updated: 2024-04-19
source: 有道云笔记迁移
tags:
  - 学习笔记
  - Java
---

# 第十三章-File类与IO流

## 13.1 java.io.File类的使用
- File类代表磁盘或者网络中的文件与文件夹（万事万物皆对象）

### 13.1.1 构造器
- `public File(String pathname)  //这里的path可以是绝对路径，也可以是相对路径(@Test中相对module，main()中对于的是project)`
- `public File(String parent, String child)  `：以parent为父路径，child为子路径创建File对象。
- `public File(File parent, String child) `：根据一个父File对象和子文件路径创建File对象
- 此处路径分隔符使用`\\`(转义字符)，或是直接使用`/`也可以

### 13.1.2 常用方法
1. 文件的一些基本信息
- public String getName() ：获取名称
- public String getPath() ：获取路径
- public String getAbsolutePath()：获取绝对路径
- public File getAbsoluteFile()：获取绝对路径表示的文件
- public String getParent()：获取上层文件目录路径。若无，返回null
- public long length() ：获取文件长度（即：字节数）。不能获取目录的长度。
- public long lastModified() ：获取最后一次的修改时间，毫秒值
> 在Java中，当你new 一个 File 对象时，它并不要求此时对应的文件或目录在磁盘上真实存在:
> 如果File对象代表的文件或目录存在，则File对象实例初始化时，就会用硬盘中对应文件或目录的属性信息（例如，时间、类型等）为File对象的属性赋值;
> 否则除了路径和名称，File对象的其他属性将会保留默认值。
![image](https://github.com/luobin7/-java-/raw/main/%E7%AC%AC15%E7%AB%A0_File%E7%B1%BB%E4%B8%8EIO%E6%B5%81/images/image-20220412215446368.png)


2. 列出文件的下一级
- public String[] list() ：返回一个String数组，表示该File目录中的所有子文件或目录的名字。
- public File[] listFiles()：返回一个File数组，表示该File目录中的所有的子文件或目录。

3. 重命名
- public boolean renameTo(File dest):把文件重命名为指定的文件路径。
> 这个是直接修改磁盘上文件和目录的名字
4. 判断功能的方法
- `public boolean exists()` ：此File表示的文件或目录是否实际存在。
- `public boolean isDirectory()` ：此File表示的是否为目录。
- `public boolean isFile()` ：此File表示的是否为文件。
- `public boolean canRead()` ：判断是否可读
- `public boolean canWrite()` ：判断是否可写
- `public boolean isHidden()` ：判断是否隐藏
5. 创建、删除功能
- `public boolean createNewFile()` ：创建文件。若文件存在，则不创建，返回false。
- `public boolean mkdir()` ：创建文件目录。如果此文件目录存在，就不创建了。如果此文件目录的上层目录不存在，也不创建。
- `public boolean mkdirs()` ：创建文件目录。如果上层文件目录不存在，一并创建。
- `public boolean delete()` ：删除文件或者文件夹 删除注意事项：① Java中的删除不走回收站。② 要删除一个文件目录，请注意该文件目录内不能包含文件或者文件目录。
> 这里的修改方法为什么都做成boolean类：返回结果以查看修改操作是否成功

## 13.2 IO流

### 13.2.1 IO原理
- 再Java中，数据的输入输出操作以流(stream)的方式进行
![image](https://github.com/luobin7/-java-/raw/main/%E7%AC%AC15%E7%AB%A0_File%E7%B1%BB%E4%B8%8EIO%E6%B5%81/images/image-20220503123117300.png)
- I/O是input/output的缩写，意指程序与存储设备之间的数据传输

### 13.2.2 流的分类
- 按照数据流向分类：输入与输出
    - 输入流：以InputStream、Reader结尾
    - 输出流：以OutputStream、Writer结尾
- 按存储单位分类
    - 字节流：InputStream、OutputStream（视频、图像、音频等二进制数据）
    - 字符流：Reader，Writer（处理文本居多）
- 按IO流角色不同分类
    - 节点流：直接从数据源或目的地读写
    - 处理流：从已存在的流上读取

### 13.2.3 流的API
- IO流共涉及40多个类，都是由以下几个基类派生的


| （抽象基类） | 输入流 | 输出流 |
| --- | --- | --- |
| 字节流 | InputStream | OutputStream |
| 字符流 | Reader | Writer |

- 由这四个类派生出来的子类名称都是以其父类名作为子类名后缀
    - 在使用时，我们不会将这些基类实例化
![image](https://github.com/luobin7/-java-/raw/main/%E7%AC%AC15%E7%AB%A0_File%E7%B1%BB%E4%B8%8EIO%E6%B5%81/images/image-20220412230501953.png)
- 常用的节点流
    - 文件流： FileInputStream、FileOutputStrean、FileReader、FileWriter
    - 字节/字符数组流： ByteArrayInputStream、ByteArrayOutputStream、CharArrayReader、CharArrayWriter
        - 对数组进行处理的节点流（对应的不再是文件，而是内存中的一个数组）。
- 常用的处理流：
    - 缓冲流：BufferedInputStream、BufferedOutputStream、BufferedReader、BufferedWriter
        - 增加缓冲功能，避免频繁读写硬盘，进而提升读写效率。
    - 转换流：InputStreamReader、OutputStreamReader
        - 作用：实现字节流和字符流之间的转换。
    - 对象流：ObjectInputStream、ObjectOutputStream
        - 作用：提供直接读写Java对象功能

## 13.3 节点流：FileReader\FileWriter

- Java提供一些字符流类，以字符为单位读写数据，专门用于处理文本文件。不能操作图片，视频等非文本文件。

#### 13.3.1 字符输入流FileReader

- `java.io.Reader`抽象类是用于读取字符流的所有类的父类.以下是Reader类定义的基本方法
    - `public int read():` 从输入流读取一个字符。 虽然读取了一个字符，但是会自动提升为int类型。返回该字符的Unicode编码值。如果已经到达流末尾了，则返回-1。(读取到之后可以用(char)转换为原来的字符)
    - `public int read(char[] cbuf)：` 从输入流中读取一些字符，并将它们存储到字符数组 cbuf中 。每次最多读取cbuf.length个字符。返回实际读取的字符个数。如果已经到达流末尾，没有数据可读，则返回-1。
    - `public int read(char[] cbuf,int off,int` len)：从输入流中读取一些字符，并将它们存储到字符数组 cbuf中，从cbuf[off]开始的位置存储。每次最多读取len个字符。返回实际读取的字符个数。如果已经到达流末尾，没有数据可读，则返回-1。//这个用的稍微多一点点
    - `public void close()` ：关闭此流并释放与此流相关联的任何系统资源。//流的操作在完成时必须调用close()释放资源
- `java.io.FileReader` 类则用于读取字符文件，构造时使用系统默认的字符编码和默认字节缓冲区。
  - FileReader(File file)： 创建一个新的 FileReader ，给定要读取的File对象。
  - FileReader(String fileName)： 创建一个新的 FileReader ，给定要读取的文件的名称。


#### 13.3.2 字符输出流：FileWriter
- `java.io.Writer` 抽象类是表示用于写出字符流的所有类的超类，将指定的字符信息写出到目的地。它定义了字节输出流的基本共性功能方法。
    - `public void write(int c)` ：写出单个字符。
    - `public void write(char[] cbuf) ：`写出字符数组。
    - `public void write(char[] cbuf, int off, int len)` ：写出字符数组的某一部分。off：数组的开始索引；len：写出的字符个数。
    - `public void write(String str)` ：写出字符串。
    - `public void write(String str, int off, int len)` ：写出字符串的某一部分。off：字符串的开始索引；len：写出的字符个数。
    - `public void flush()` ：刷新该流的缓冲。
    - `public void close()` ：关闭此流。
- `java.io.FileWriter` 类用于写出字符到文件，构造时使用系统默认的字符编码和默认字节缓冲区。
    - `FileWriter(File file)：` 创建一个新的 FileWriter，给定要读取的File对象。
    - `FileWriter(String fileName)：` 创建一个新的 FileWriter，给定要读取的文件的名称。
    - `FileWriter(File file,boolean append)：` 创建一个新的 FileWriter，指明是否在现有文件末尾追加内容。`// 覆盖or续写`

### 13.4 字节流：FileInputStream与FileOutputStream
- 字节流，用来处理一些非文本文件

### 13.4.1 FileInputStream

- FileInputStream的超类是`java.io.InputStream`,它定义了字节输入流的基本共性功能方法。
    - `public int read()：` 从输入流读取一个字节。返回读取的字节值。虽然读取了一个字节，但是会自动提升为int类型。如果已经到达流末尾，没有数据可读，则返回-1。
    - `public int read(byte[] b)：` 从输入流中读取一些字节数，并将它们存储到字节数组 b中 。每次最多读取b.length个字节。返回实际读取的字节个数。如果已经到达流末尾，没有数据可读，则返回-1。
    - `public int read(byte[] b,int off,int` len)：从输入流中读取一些字节数，并将它们存储到字节数组 b中，从b[off]开始存储，每次最多读取len个字节 。返回实际读取的字节个数。如果已经到达流末尾，没有数据可读，则返回-1。
    - `public void close()` ：关闭此输入流并释放与此流相关联的任何系统资源。
- `java.io.FileInputStream` 类是文件输入流，从文件中读取字节。
    - `FileInputStream(File file)`： 通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的 File对象 file命名。
    - `FileInputStream(String name)：` 通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的路径名 name命名。

### 13.4.2 FileOputStream
- `java.io.OutputStream` 抽象类是表示字节输出流的所有类的超类，将指定的字节信息写出到目的地。它定义了字节输出流的基本共性功能方法。
    - `public void write(int b)` ：将指定的字节输出流。虽然参数为int类型四个字节，但是只会保留一个字节的信息写出。
    - `public void write(byte[] b)`：将 b.length字节从指定的字节数组写入此输出流。
    - `public void write(byte[] b, int off, int len)` ：从指定的字节数组写入 len字节，从偏移量 off开始输出到此输出流。
    - `public void flush()`  ：刷新此输出流并强制任何缓冲的输出字节被写出。
    - `public void close()` ：关闭此输出流并释放与此流相关联的任何系统资源。
- `java.io.FileOutputStream` 类是文件输出流，用于将数据写出到文件。
    - `public FileOutputStream(File file)`：创建文件输出流，写出由指定的 File对象表示的文件。
    - `public FileOutputStream(String name)`： 创建文件输出流，指定的名称为写出文件。
    - `public FileOutputStream(File file, boolean append)`： 创建文件输出流，指明是否在现有文件末尾追加内容。

- 字符流只能进行文本文件操作
- 字节流一般适用非文本文件，如果是文本复制任务也可以使用字节流（无中文的情况）

## 13.5 缓冲流
- 为了提高数据读写的速度，Java API提供了带缓冲功能的流类：缓冲流。
- 缓冲流要“套接”在相应的节点流之上，根据数据操作单位可以把缓冲流分为：
    - 字节缓冲流：`BufferedInputStream，BufferedOutputStream`
    - 字符缓冲流：`BufferedReader，BufferedWriter`
- 缓冲流的基本原理：在创建流对象时，内部会创建一个缓冲区数组（缺省使用8192个字节(8Kb)的缓冲区），通过缓冲区读写，减少系统IO次数，从而提高读写的效率。
![image](https://github.com/luobin7/-java-/raw/main/%E7%AC%AC15%E7%AB%A0_File%E7%B1%BB%E4%B8%8EIO%E6%B5%81/images/image-20220514183413011.png)

### 13.5.1 构造器
- public BufferedInputStream(InputStream in) ：创建一个 新的字节型的缓冲输入流。
- public BufferedOutputStream(OutputStream out)： 创建一个新的字节型的缓冲输出流。

### 13.5.2 流程
1. 分别创建输入输出字节流与缓冲流，将字节流套接在缓冲流上
2. 与字节流一样的读取写入操作(bbuffer)
3. 关闭外层缓冲流（会将内层的字节流一并关闭）

```
public void copyFileWithBufferedStream(String srcPath,String destPath){
    FileInputStream fis = null;
    FileOutputStream fos = null;
    BufferedInputStream bis = null;
    BufferedOutputStream bos = null;
    try {
        //1. 造文件
        File srcFile = new File(srcPath);
        File destFile = new File(destPath);
        //2. 造流
        fis = new FileInputStream(srcFile);
        fos = new FileOutputStream(destFile);

        bis = new BufferedInputStream(fis);
        bos = new BufferedOutputStream(fos);

        //3. 读写操作
        int len;
        byte[] buffer = new byte[100];
        while ((len = bis.read(buffer)) != -1) {
            bos.write(buffer, 0, len);
        }
        System.out.println("复制成功");
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        //4. 关闭资源(如果有多个流，我们需要先关闭外面的流，再关闭内部的流)
        try {
            if (bos != null)
                bos.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        try {
            if (bis != null)
                bis.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }

    }
}
```

### 13.5.3 缓冲流的一些特有方法
- BufferedReader：`public String readLine()`:读一行文字
    - 一行文本的结束符可以是一个换行符`('\n')`，一个回车符`('\r')`，或者是一个回车后跟一个换行`('\r\n')`。

```
    @Test
    public void test1() throws IOException {
        BufferedReader br = new BufferedReader(new FileReader("src/out.txt"));
        String line;
        while ((line = br.readLine()) != null){
            System.out.println(line);
        }
        br.close();
    }
```

- BufferedWriter：`public void newLine():` 写一行行分隔符,由系统属性定义符号。

```
    @Test
    public void test1() throws IOException {
        BufferedReader br = new BufferedReader(new FileReader("src/java.txt"));
        BufferedWriter bw = new BufferedWriter(new FileWriter("src/newout.txt"));
        String line;
        while ((line = br.readLine()) != null){
            bw.write(line);
            bw.newLine();
        }
        br.close();
        bw.close();
    }
```
## 13.6 转换流
### 13.6.1 转换流的理解
- 转换流是字节与字符间的桥梁，`InputStreamReader-OutputStream`
- 目标就是将一个字节流包装起来当一个字符流对象用
![image](https://github.com/luobin7/-java-/raw/main/%E7%AC%AC15%E7%AB%A0_File%E7%B1%BB%E4%B8%8EIO%E6%B5%81/images/image-20220412231533768.png)

### 13.6.2 InputStreamReader 与 OutputStreamWriter
- 转换流`java.io.InputStreamReader`是字符流初始类Reader的子类，它读取字节，并使用指定的字符集将其解码为字符。它的字符集可以由名称指定，也可以接受平台的默认字符集。（转化成谁用谁的子类）
```
InputStreamReader(InputStream in): 创建一个使用默认字符集的字符流对象（内部放一个字节流，例如FileInputStream）。
InputStreamReader(InputStream in, String charsetName): 创建一个指定字符集的字符流。
// 其余操作和常规的字符流一致
```
- 转换流`java.io.OutputStreamWriter` ，是Writer的子类，是从字符流到字节流的桥梁。使用指定的字符集将字符编码为字节。它的字符集可以由名称指定，也可以接受平台的默认字符集。

```
OutputStreamWriter(OutputStream in): 创建一个使用默认字符集的字符流。
OutputStreamWriter(OutputStream in,String charsetName): 创建一个指定字符集的字符流。
```

### 13.6.3 字符编码和字符集
- 编码：字符-字节；解码：字节-字符
- 字符集：Charset
    - ASCII字符集
    - ISO-8859-1字符集
    - GBX：GB就是国标，目前最常用的GBK，向下兼容ASCII
    - Unicode：所有文字使用2个字节统一编码，分为UTF-8,UTF-16,UTF-32三种具体的编码方案（每次以多少位bit作为一个编码单元）
        - UTF-8中128个US-ASCII字符，只需一个字节编码；拉丁文等字符，需要二个字节编码；大部分常用字（含中文），使用三个字节编码。其他极少使用的Unicode辅助字符，使用四字节编码。

## 13.7 数据流与对象流
### 13.7.1 数据流
- 数据流用来存储我们在内存中定义的变量，例如：

```
int age = 300;
char gender = '男';
int energy = 5000;
double price = 75.5;
boolean relive = true;

String name = "巫师";
Student stu = new Student("张三",23,89);
```
- 数据流：DataOutputStream、DataInputStream
    - DataOutputStream：允许应用程序将基本数据类型、String类型的变量写入输出流中
    - DataInputStream：允许应用程序以与机器无关的方式从底层输入流中读取基本数据类型、String类型的变量。
- 数据流常用的方法：

```
  byte readByte()                short readShort()
  int readInt()                  long readLong()
  float readFloat()              double readDouble()
  char readChar()				 boolean readBoolean()					
  String readUTF()               void readFully(byte[] b)
 对象流DataOutputStream中的方法：将上述的方法的read改为相应的write即
```
- 数据流的弊端：只支持Java基本数据类型和字符串的读写，而不支持其它Java对象的类型。而`ObjectOutputStream`和`ObjectInputStream`既支持Java基本数据类型的数据读写，又支持Java对象的读写，所以重点介绍对象流`ObjectOutputStream`和`ObjectInputStream`


### 13.7.2 序列化与反序列化
- 序列化机制是对象流的理论基础，它允许把内存中的Java对象转换为二进制流保存在硬盘中
![image](https://github.com/luobin7/-java-/raw/main/%E7%AC%AC15%E7%AB%A0_File%E7%B1%BB%E4%B8%8EIO%E6%B5%81/images/image-20220503123328452.png)

- 序列化是 RMI（Remote Method Invoke、远程方法调用）过程的参数和返回值都必须实现的机制，而 RMI 是 JavaEE 的基础。因此序列化机制是 JavaEE 平台的基础。




### 13.7.3 对象流
- `ObjectOutputStream、ObjectInputStream`是java中最常用的两种对象类，它可以把Java的对象写入到数据源与从数据源中还原出来。
- 他们也是实现序列化与反序列化过程主要使用的类
1. 对象流API
- 构造器：套接在一个字节流上
-  `public ObjectOutputStream(OutputStream out)` ： 创建一个指定的ObjectOutputStream。

```
//FileOutputStream的构造器
FileOutputStream fos = new FileOutputStream("game.dat");
ObjectOutputStream oos = new ObjectOutputStream(fos);
```

- `public ObjectInputStream(InputStream in) `： 创建一个指定的ObjectInputStream。

```
FileInputStream fis = new FileInputStream("game.dat");
ObjectInputStream ois = new ObjectInputStream(fis);
```


- `ObjectOutputStream`中的方法
    - `public void writeBoolean(boolean val)`：写出一个 boolean 值。
    - `public void writeByte(int val)`：写出一个8位字节
    - `public void writeShort(int val)`：写出一个16位的 short 值
    - `public void writeChar(int val)`：写出一个16位的 char 值
    - `public void writeInt(int val)`：写出一个32位的 int 值
    - `public void writeLong(long val)`：写出一个64位的 long 值
    - `public void writeFloat(float val)`：写出一个32位的 float 值。
    - `public void writeDouble(double val)`：写出一个64位的 double 值
    - `public void writeUTF(String str)`：将表示长度信息的两个字节写入输出流，后跟字符串 s 中每个字符的 UTF-8 修改版表示形式。根据字符的值，将字符串 s 中每个字符转换成一个字节、两个字节或三个字节的字节组。注意，将 String 作为基本数据写入流中与将它作为 Object 写入流中明显不同。 如果 s 为 null，则抛出 NullPointerException。
    - `public void writeObject(Object obj)`：写出一个obj对象
    - public void close() ：关闭此输出流并释放与此流相关联的任何系统资源

- ObjectInputStream中的方法与ObjectOutputStream类似：基本数据类型+readObject

### 17.3.4 如何实现序列化
- 要让我们自定义的类支持序列化，我们要在类中实现`java.io.Serializable`  接口
    - 如果不在变量内声明一个全局常量用于控制自定义类的修改，系统会自动生成一个
    - 如果对象的某个属性也是引用数据类型，那么如果该属性也要序列化的话，也要实现Serializable 接口
    - 该类的所有属性必须是可序列化的。如果有一个属性不需要可序列化的，则该属性必须注明是瞬态的，使用transient 关键字修饰。
    - 静态（static）变量的值不会序列化。因为静态变量的值不属于某个对象。

## 17.4 一些其他的输入输出流
### 17.4.1 标准的输入输出流
- System.in和System.out定义了系统标准的输入（默认的是键盘）和输出装备（默认显示器）
- System.in的类型是InputStream
System.out的类型是PrintStream，其是OutputStream的子类FilterOutputStream 的子类
- 重定向：通过System类的setIn，setOut方法对默认设备进行改变。(从显示器改为某一个文本文件)
    - `public static void setIn(InputStream in)`
    - `public static void setOut(PrintStream out)`

```
BufferedReader bufr = new BufferedReader(new InputStreamReader(System.in));
BufferedWriter bufw = new BufferedWriter(new OutputStreamWriter(System.out));
```

- 在下一章节介绍一下PrintStream打印流

### 17.4.2 打印流PrintStream和PrintWriter()
- PrintStream和PrintWriter，前者是字节流，是FilterOutputStream的子类，为字节流；后者则是Writer的子类，为字符流
- ![image](https://github.com/luobin7/-java-/raw/bbd4d91bc59c57ac62d01c8d6b08b12871ec0072/%E7%AC%AC15%E7%AB%A0_File%E7%B1%BB%E4%B8%8EIO%E6%B5%81/images/image-20220131021502089.png)
- 他们的主要作用就是与重定向一起使用，从而更快捷的通过print指令来将内容输出到指定的文件中

## 相关笔记
所属索引：[[JavaSE-MOC]]
