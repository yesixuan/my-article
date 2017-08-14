---
title: IO流
tags: ["java","IO流"]
date: 2017-08-13 08:07:00
categories:
- 后端
- java
- 基础
password: false
---
> 有了IO流，我们才拥有了对硬盘上的文件进行读写的功能。当然，这只是一个开始...

<!-- more -->
## File类

### 概览
创建一个代表一个文件或者文件夹对象，也就是你要操作的目标。    
```java
// 创建一个文件对象（不管是不是真实存在）
File file = new File("F:\\a.txt"); // Linux里面是一个正斜杠（windows也是支持的）
// 判断文件对象是否存在
file.exists();
// 获取系统的目录分隔符
File.separator();
```

### 构造方法
```java
// 指定文件或文件夹，创建文件对象
File(String pathname);
// 将路径拆开来（应用场景：先要操作父文件对象，再操作子文件）
File(File parent, String child);
// 没太多意义，纯粹拆开路径
File(String parent, String child);
```
注意：使用相对路径时，当前路径指的是项目根目录。  

### File类中常用方法
#### 创建
```java
File file = new File("F:\\a.txt");
File dir = new File("F:\\a");
// 基于文件对象创建一个文件（会抛出异常，返回Boolean）
file.createNewFile();
// 基于文件对象创建一个单级文件夹
dir.mkdir();
// 创建一个多级文件夹
dir.mkdirs();
// 重命名（包含剪切）
file.renemeTo(destFile);
```
#### 删除
```java
// 删除文件或空文件夹
delete();
// 在虚拟机终止时，删除此抽象路径下的文件或目录，保证程序异常时创建的临时文件也可以删除
deleteOnExit()
```
#### 判断
```java
exists();
isFile();
isDirectory();
isHidden();
isAbsolute(); // 判断该路径是否为绝对路径
```
#### 获取
```java
getName();
getPath();
getAbsolutePath();
// 获取文件大小（字节数），如果文件不存在或者是文件夹则返回0L
length();
getParent();
lastModified();
// 列出系统根目录
static File[] listRoots();
// 返回指定目录中的子文件或子目录
list();
// 返回指定目录中符合过滤条件的子文件或子目录
list(Filename filter);
// 列出指定目录下的文件或目录对象（File实例）
listFiles();
// 返回指定目录下符合过滤条件的子文件或子目录
listFiles(Filename filter);
```

### 实例
场景：分开列出目录中所有子文件名与子目录名。  
```java
public static viod listAllFilesAndDirs(String path) {
  File dir = new File(path);
  File[] name = dir.listFiles();
  List<File> fileList = new ArrayList<File>();
  List<File> dirList = new ArrayList<File>();
  for(int i = 0; i < name.length; i++) {
    File file = names[i];
    if(file.isFile) {
      fileList.add(file);
    }else if(file.isDirectory) {
      dirList.add(file);
    }
  }
  System.out.println("子文件：");
  for(int i = 0; i < fileList.size(); i++) {
    System.out.println("\t" + fileList[i].getName());
  }
  System.out.println("子目录：");
  for(int i = 0; i < dirList.size(); i++) {
    System.out.println("\t" + dirList[i].getName());
  }
}
```

## 字节流
上面我们已经知道File对象封装的是文件或者路径属性，但是不包含向（从）文件读（写）数据的方法，此时就该是IO流粉墨登场的时候了。  
### 输入流
#### read()  
这种方式料率底下，不推荐使用。  
```java
/* 一次读取一个字节，读到文末返回-1 */
private viod showContent(String path) throws IOException {
  // 打开流管道
  FileInputStream fis = new FileInputStream(path);
  int len;
  while(len = fis.read() != -1) {
    System.out.println((char)len);
  }
  fis.close();
}
```
#### read(byte[] b)
使用read()的时候，流需要读一次就处理一次。read(byte[] b)可以将读到的数据装入字节数组中，一次性地操作数组，可以提高效率。  
read()就类似你从井里一次舀一杯水送到房间里给别人喝；read(byte[] b)就相当于你从井里一次舀一杯水先放到桶子里，等舀满一桶水再将这桶水运到房间里给别人喝。  
```java
private viod showContent(String path) throws IOException {
  FileInputStream fis = new FileInputStream(path);
  byte[] byt = new byte[1024];
  int len = 0;
  while((len = fis.read(byt))! = -1) {
    // 字节数组里面存的是二进制数据，要将之解析出来需要借助字符串的构造方法
    System.out.println(new String(byt, 0, len));
  }
}
```
#### read(byte[] b, int off, int len)
其实就是把数组的一部分当作流的容器来使用。告诉容器从什么地方开始装多少。

### 输出流
输出流基本就是用来写入数据的。  
#### write(int b)
write(int b)一次写出一个字节。  
虽然write(int b)接收的是int类型参数，但是write(int b)的常规协定是：**向输入流写入一个字节，要写入的字节是参数的低八位，高24个高位将将被忽略。**
#### write(byte[] b)
write(byte[] b)相比write(int b)效率更高。原因就不多说了。  
我们只需要将字符串转化为字节数组，然后通过该方法就可以一次写入多个字节。  
创建输出字节流对象时，如果不想覆盖已存在内容，而是追加内容只需如此这般：`new FileOutputStream(path, true)`
```java
private static void writeTxtFile(String path) throwss IOException {
  FileOutputStream fos = new FileOutputStream(path);
  byte[] byt = "要插入的数据".getBytes();
  fos.write(byt);
  fos.close();
}
```

### 字节流文件拷贝
```java
public static copyFile(String srcPath, String destPath) throwss IOException {
  // 打开输入输出流
  FileInputStream fis = new FileInputStream(srcPath);
  FileOutputStream fos = new FileOutputStream(destPath);
  // 读取和写入信息
  int len = 0;
  byte[] byt = [1024];
  while((len = fis.read(byt)) != -1) {
    fos.write(byt, 0, length);
  }
  // 关闭流
  fis.close();
  fos.close();
}
```

### 字节流的异常处理
上述例子中，我们使用了抛出异常的方式来处理异常。但实际上，这样做是不合适的。  
例如第一个通道关闭出错会导致第二个通道无法关闭。正确的做法是使用`try--catch--finelly`语句块来处理异常。  

### 字节缓冲流
字节缓冲流内部有一个缓冲区，其实内部也是封装了字节数组，默认的字节是8192。  
缓冲区输入流与缓冲区输出流要配合使用。首先缓冲区输入流会将读取到的数据写入缓冲区，当缓冲区满时，或者调用flush()方法，缓冲输出流会将数据写出。  
```java
public static viod copyFile(String srcPath, String destPath) throws IOException {
  // 打开输入流，输出流
  FileInputStream fis = new FileInputStream(srcPath);
  FileOutputStream fos = new FileOutputStream(destPath);
  // 使用缓冲流
  BufferedInputStream bis = new BufferedInputStream(fis);
  BufferedOutputStream bos = new BufferedOutputStream(fos);
  // 读取和写入信息
  int len = 0;
  while((len = bis.read()) != -1) {
    bos.write(len);
  }
  // 关闭流
  bis.close();
  bos.close();
}
```

## 字符流（FileReader & FileWwiter）
字节流以字节为单位，而字符流是以字符为单位。更方便地处理文本文档。  
### 常见的码表
计算机不管是存文本文件还是二进制文件本质上都是存的二进制数据。如果是文本文件的话，计算机需要一套规则才能将二进制的数据翻译成我们能看懂的字符。这套规则就是指的码表。  
- ASCII：美国标准信息交换码。一个字符占一个字节，用一个字节的7位可以表示
- ISO8859-1：拉丁码表。欧洲码表，用一个字节的8位表示
- GBG2312： 英文占一个字节，中文占两个字节
- GBK：中国的中文码表升级，融合了更多的中文文字符号
- Unicode：国际标准码规范。所有文字都用两个字节来表示（java就是用的这个）
- UTF-8：英文一个字节，中文三个字节  

### Reader
#### 方法
`int reader();`读取一个字符。返回的是读到的那个字符。如果读到流的末尾则返回-1；  
`int read(char[]);`将读到的字符存入指定的数组中，返回的是读到的字符个数，也就是往数组里装的元素的个数。读到流的末尾则返回-1；
`close();`使用完毕后，进行资源的释放；  
```java
public static void readFileByReader(String path) throws  Exception {
  Reader reader = new FileReader(path);
  int len = 0;
  while((len = reader.read()) != -1) {
    System.out.println((char)len);
  }
  reader.close();
}
```

### Writer
`write(ch)`将一个字符写入到流中。  
`write(char[])`将一个字符数组写入到流中。  
`write(String)`将字符串写入到流中。  
`flush()`刷新流，将流中的数据刷新到目的地中，流还存在。  
`close()`关闭资源。关闭前会先调用`flush()`，刷新流中数据到目的地，然后流关闭。  
```java
public static void writeToFile(String path) throws Exception {
  // 这里的第二个参数表示是否为追加
  Write write = new FileWiter(path, true);
  write.write('中');
  write.write("世界".toCharArray());
  write.write("中国");
  // 这里没有调用flush()是因为close()会调用flush()
  write.close();
}
```

### 用字符流拷贝文本文件
```java
public static void copyFile(String path1, String path2) throws Exception {
  Reader reader = new FileReader(path1);
  Writer writer = new FileWiter(path2);
  int ch = -1;
  char[] arr = new char[1024];
  while((ch = reader.reader(arr)) != -1) {
    writer.writer(arr, 0, ch);
  }
  reader.close();
  writer.close();
}
```

### 字符流的缓冲区
缓冲的存在是为了增强流的功能而存在，所以在建立缓冲区对象时，先要有流对象存在。  
缓冲区的出现提高了对流的操作效率。原理就是将数组进行封装。  
```java
private static void copyFile(File srcFile, File destFile) throws IOException {
  // 创建输入输出流
  FileReader fr = new FileReader(srcFile);
  FileWriter fw = new FileWriter(destFile);
  // 创建字符输出流
  BufferedReader br = new BufferedReader(fr);
  BufferedWriter bw = new BufferedWriter(fw);
  // 拷贝（可以一行一行地读）
  String line = null;
  while((line = br.readLine()) != null) {
    // 一次写出一行
    bw.write(line);
    // 刷新缓冲
    bw.flush();
    // 换行，readLine方法默认没有换行，需要手动换行
    bw.newLine();
  }
  // 关闭流
  br.close();
  bw.close();
}
```
