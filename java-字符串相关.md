---
title: java中的字符串
tags: ["java","字符串"]
date: 2017-08-11 09:09:00
categories:
- 后端
- java
- 基础
---
> java开篇之作，来聊一聊java中的字符串相关...

<!-- more -->
## 字符串比较
### 创建过程
字符串的常用创建方式有两种。一种是字面量的方式创建，另一种是使用new关键字创建。  
第一种创建方式，jvm会首先在字符串常量池中检查是否存在该字符串。如果不存在，则在字符串常量池中创建字符串对象，返回其内存地址；如果存在，则直接返回该对象的内存地址。  
第二种创建方式，jvm也会首先在字符串常量池中检查是否已经存在，如果已存在，则将其拷贝到堆内存中，并返回堆内存中的内存地址。如果不存在，就会在堆内存中创建字符串对象，返回其内存地址。  
### 比较符
使用`==`比较时，比较的是内存地址。  
使用`equals()`比较时，比的是字符串的内容（字符串类重写了继承自Object的equals()）。  
### 总结
1. 使用字面量方式创建的字符串对象在字符串常量池中  
2. 使用new关键字创建的对象在堆内存中  
3. 字符串之间的比较使用`equals()`就好  
4. 比较一个变量是否为某个字符串时，使用`字符串.equals(变量)`来判断，避免报错  

## String类的方法
### 构造方法
```java
/* 使用字节数组构建字符串（使用平台默认编码规则） */
byte[] buf = {97, 98, 99};
String str = String(buf); // [abc]
// 指定码表
String(byte[] bytes, Charset charset);
// 指定开始位置使用的位置索引值与使用的字符个数（最后还可以加一个参数，用来指定码表）
String(byte[] bytes, int offset, int length);

/* 使用字符数组构建一个字符串 */
char[] arr = {'a', 'b', 'c'};
str = new String(arr);
// 指定片段构造
String(char[] value, int offset, int count);

/* 使用int类型数组构建字符串（类byte数组） */
int[] arr2 = {65, 66, 67};
str = new String(arr2, 0, 3);
```
### 获取方法
```java
// 获取字符串长度
int length();
// 获取特定位置的字符
char charAt(int index);
// 获取特定字符的位置（从头检索）
int indexOf(String str);
// 获取特定字符的位置（从屁股检索）
int lastIndexOf(int ch)
```
### 判断方法
```java
// 判断是否以指定字符结束
boolean endsWidth(String str);
// 判断是否为空字符串
boolean isEmpty();
// 是否包含指定序列
boolean contains(CharSequences);
// 是否相等
boolean equals(String str);
// 忽略大小写比较
boolean equalsIgnoreCase(String anotherString);
```
### 转换方法
```java
// 将字符数组转换为字符（可以指定开始位置与长度）
String(char[] value);
// 将字符串转换为字符数组（字符串反转时要用到）
char[] toCharArray();
```
### 其他方法
```java
String replace(char oldChar, char newChar); // 替换
String[] split(String regex); // 切割，返回数组
String substring(int beginIndex, int endIndex); // 跟js中的同名方法一致
String toUpperCase();
String toLowerCase();
String trim(); // 去空格
```

## StringBuffer与StringBuilder
由于String是不可变的，所以在需要频繁改变字符串对象的应用中，需要使用可变的字符串缓冲区类。  
默认缓冲区的容量是16。  
StringBuffer与StringBuilder的用法类似，区别在于StringBuilder是线程不安全的，但是效率要高。  
### 常用方法
```java
/* 添加方法 */
StringBuffer("jack"); // 在创建对象的时候赋值
append(); // 在缓冲区尾部添加新的文本对象
insert(); // 在指定的下标位置添加新的文本对象

/* 查看 */
toString(); // 返回这个容器的字符串
indexOf(String str);

/* 修改 */
substring(int start); // 从开始位置截取字符串
replace(int start, int end, String str);
setCharAt(int index, char ch); // 指定索引位置替换一个字符

/* 删除 */
delete (int start, int end);
deleteCharAt(int index);

/* 反序 */
reverse();
```
