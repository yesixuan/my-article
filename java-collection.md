---
title: 集合
tags: ["java","collection"]
date: 2017-08-11 19:11:00
categories:
- 后端
- java
- 基础
---
> 之前一直很费解java中那么严格的数组怎么能满足开发中的各种需求，也一直为javascript那么灵活的数据类型而庆幸。直到了解了集合这个东西的时候才知道很多东西是想当然了...

<!-- more -->
## 概览
Collection是与数组一个阶层的数据类型。不同的是，数组是定长并且数据类型固定。而Collection就没有这样的限制。    
Collection的体系如下：    
----| Collection 单列集合类的根接口      
&emsp;&emsp;----| List 描述可以有重复元素的集合  
&emsp;&emsp;&emsp;&emsp;----| ArrayList 实现了List接口的实现类，特点是查询快，增删慢  
&emsp;&emsp;&emsp;&emsp;----| LinkedList 链表存储，特点是查询慢，增删快  
&emsp;&emsp;----| Set接口 不可以有重复元素的集合  
&emsp;&emsp;&emsp;&emsp;----| HashSet hash表存储，新增时，先判断hashcode，hashcode相同再调用equals方法判断   
&emsp;&emsp;&emsp;&emsp;----| TreeSet 二叉树结构存储，会对集合内的元素进行排序    

### collection常用方法
- 增加
  - add(E e)
  - addAll(Collection c)
- 删除
  - clear() ：清空集合元素
  - remove(Object 0) ：删除某元素（返回boolean）
  - removeAll(Collection c) ：删除两个集合中的交集元素（返回boolean）
  - retainAll(Collection c) ：保留两个集合中的交集元素（返回boolean）
- 查看
  - contains(Object o)
  - containsAll(Collection c)
  - isEmpty()
  - size()
- 迭代
  - toArray() ：将集合中的元素存储到对象数组中返回
  - iterator() ：返回一个实现了Iterator接口的实现类对象  

### Iterator接口
当获取到集合中的迭代器的时候，那么迭代器对象就会有一个游标指向了集合中的第一个元素。  
常用方法：hasNext()、next()、remove()。  
remove()移除迭代器最后一次返回的元素（即最后一个next()返回的）。  
```java
// Collection是接口，所以创建的时候都是创建它的实现类对象
Collection c = new ArrayList();
// 获取实现了Iterator接口的实现类对象
Iterator it = c.iterator();
// 遍历
while(it.hasNext()) {
  System.out.println(it.next());
}
```

### 注意  
1. 要以对象的形式查看集合元素，需要重写集合元素中的toString();  
2. ArrayList实现的contains()判断的依据实际上是调用了每个每个元素的equals()。equals()默认比较内存地址，很多时候，我们需要重写元素的equals();
3. isEmpty()判断集合中如果有null，则判定结果也是非空的;
4. hasNext()实际上是问当前游标有没有指向一个元素;
5. next()首先返回当前游标指向的元素，然后游标向下移动一个单位;

## List
实现了List接口类的添加元素都是有序的，并且可以重复。  
有序：在集合中所谓的“有序”不是指自然顺序，而是指添加进去的顺序与存储的顺序一致。
### List接口特有方法
- 增加
  - add(int index, E element)
  - addAll(int index, Collection c)
- 删除
  - remove(int index)
- 修改
  - set(int index, E element)
- 获取（类似字符串方法）
  - get(int index)
  - indexOf(Object o) ：类似js，如果不存在则返回-1
  - lastIndexOf(Object o)
  - subList(int fromIndex, int toIndex) ：返回Lint接口的实现类对象SubList，而不是List
- 迭代
  - listIterator()  
  listIterator()特有的方法：hasPrevious()（是否有上一个元素，注意区别hasnext()）、previous()、add(E e)、set(E e)。  
  add(E e)表示将元素添加到当前游标指向的位置上。  
  set(E e)替换最后一次返回出来的元素，包括previous()和next()。  
总结：List接口下的方法大都是操作索引的（传入索引或返回索引），这一是它有序的前提下造成的现象。  
四种遍历方式：① toArray(); ② for循环配合get; ③ 遍历器顺序; ④ 遍历器逆序。  
集合对象的add()添加至集合的尾部，而List实现类对象的add()添加至游标指向的索引。在遍历过程中，使用集合对象的add()会报错，而List实现类对象的add()会跳过新添加的元素继续遍历。
注意：迭代器在迭代过程中（指在两个及以上的next()中间），不能使用集合对象改变集合的元素个数。  

### ArrayList
ArrayList无参构造方法默认容量为10。如果不够，则自动增加为原来的1.5倍。它的底层是使用了Object数组来存储元素的。  
ArrayList的特有方法: ensureCapacity(int mainCapacity)（设置长度）、trimToSize()（设置为实际长度）。两者都不常用。  
ArrayList查询快，增删慢的原因是：Object数组中，元素与元素的内存地址是连续的（查询快）；增删过程中会判断原数组长度是否足够，如果不够，则对数组进行拷贝，这一点很费时。  

### LinkedList
LinkedList的存储方式是链表式存储。类似现实生活中的链条，一环扣着一环。上个元素保存着下个元素的内存地址。正因如此，LinkedList查询慢，增删快。  
#### LinkedList特有方法
LinkedList特有方法都是跟首尾有关的：addFirst(E e)、addLast(E e)、getFirst()、getLast()、removeFirst()、removeLast()

## Set
实现了Set接口的集合类具备的特点：无序、元素不可重复。  
### HashSet
HashSet底层是使用hashTable来实现的。  
HashSet判断重复元素的判断过程是首先使用`hashCode()`判断，如果hashcode相同再调用`equals()`，如果hashCode相同，而equals不等时，两个元素将存储在同一个单元格（桶式结构，一个单元格可以看作一个桶）中。  
所以很多时候，我们要利用HashSet来去重的话，需要重写`hashcode()`。  

### TreeSet
TreeSet内部使用红黑树结构存储，具有自动排序功能。  
TreeSet默认是按照元素的自然顺序进行排序的，但是如果元素不具备自然顺序则需要我们为元素所属类实现Comparable接口的compareTo的方法或者是在创建TreeSet对象时传入比较器对象。
```java
/* 方法一，实现Comparable接口的compareTo方法 */
class Emp implements Comparable {
  // ...
  @override
  public int compareTo(Object o) {
    Emp e = (Emp)o;
    return this.salary - e.salary;
  }
}

/* 方法二，创建TreeSet对象时传入比较器对象 */
// 自定义比较器类
class AgeComparator implements Comparator {
  @override
  public int compare (Object o1, Object o2) {
    Emp e1 = (Emp)o1;
    Emp e2 = (Emp)o2;
    return e1.age - e2.age;
  }
}
// ...
// 传入TreeSet构造方法
AgeComparator ageComparator = new AgeComparator();
TreeSet tree = new TreeSet(ageComparator);
```
当TreeSet对象必备上述两种比较规则的话，将会优先使用传入比较器的方式。这里推荐使用传入比较器方式，复用性与优先级都比较高。  

## 泛型
严格来说，泛型并不属于collection这个体系。但听说collection搭配泛型口感会更佳。这里也一并来唠唠。  
```java
// <String>相当于给list这个元素贴上了一个标签，只能用来存放String类型的元素
ArrayList<String> list = new ArrayList<String>(); // 标准写法是两边都写泛型，但只写一边也没问题
```
泛型的好处：  
1. 把运行时出现的问题提前至编译时；
2. 避免无意义的强制类型转换；  

### 自定义泛型
自定义泛型可以理解为是一个数据类型的变量或者是一个数据类型的占位符。  
函数自定义泛型的格式：  
修饰符 <声明自定义泛型> 返回值类型 函数名 (形参列表...) {}  
在调用函数时才确定自定义泛型所代表的数据类型。
```java
// 如果传入的是基础数据类型，则返回的该数据类型的包装类
public static <T> print(T o) {
  return o
}
```
基础数据类型对应的包装类型：Integer、Float、Double、Character、Boolean、Byte、Short、Long。  

自定义泛型类：在类名后面加上泛型，则该类变为泛型类。类中的方法就不用重复地声明泛型了。这里泛型的确定时机是在创建对象是确定的（如果在创建泛型类对象时，没有传入泛型类型，则默认为Object类型）。  
注意：类上的泛型是不能够给静态方法使用的，此时只能在静态方法上声明泛型。  

泛型接口：与泛型类类似，泛型声明在接口名后面。在实现该接口时确定泛型类型（如果此时还不能确定泛型类型，就需要将该实现类声明为同名接口）。  

### 泛型的上下限
泛型的上下限实际上就是将泛型的指代范围缩小。
```java
// 接收Integer以及它的父类
public static void print(Collection<? super Integer>) {}
// 接受Number以及它的子类
public static void show(Collection<? extend Number>) {}
```
