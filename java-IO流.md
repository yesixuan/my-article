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
> ...

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
