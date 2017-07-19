---
title: mysql基本操作
tags: ["database"]
date: 2017-07-19 15:31:00
categories:
- 后台
- 数据库
---
> 曾经听说过这样一个说法：jQuery之于JS就好似express之于nodejs。如此这般，大家应该知道express的江湖地位了...  
 
<!-- more -->

## 准备工作

1. 安装mysql客户端
2. 安装时选择server only就行（我们只需要它的服务端）
3. 展示高级选项，设置密码
4. 安装Navicat for MYSQL客户端
5. 用navicat连接mysql（使用localhost就行）

## 基本SQL语句

### 增
INSERT INTO 表 (字段列表) VALUES (值列表)   
INSERT INTO `user_table` (`ID`, `username`, `password`) VALUES(0, 'ye', '741258')

### 删
DELETE FROM 表 WHERE 条件

### 改
UPTATE 表 SET 字段=值,字段=值 WHERE ID=xx  
UPDATE `user_table` SET password=123 WHERE ID=xx

### 查
SELECT 什么 FROM 表  
SELECT * FROM `user_table` [WHERE ID=XX] // 中括号中的条件是可选的  
SELECT ID,username FROM `user_table` // 指定要查询的特定字段  

## SQL子句

### WHERE 条件
WHERE name='xuan' // <、>、<=、>=..  
WHERE age>18 AND score<60 // 还有OR  

### ORDER 排序
ORDER BY age ASC/DESC // ASC升序、DESC降序  
ORDER BY price ASC, sales DESC // 首先按价格升序排，价格相同就按销量降序排

### GROUP 聚类（合并相同的，只保留第一条）COUNT 计数
SELECT * FROM `student_table` GROUP BY class // 班级相同的数据只保留第一条（一般来说不会单纯地用他来去重）  
SELECT class,COUNT(class) FROM `student_table` GROUP BY class // 只保留班级字段，以及每个班级多少人  
SELECT class,AVG(score) FROM `student_table` GROUP BY class // 拿到每个班的平均分（后面还可以加order by AVG(score) desc）

### LIMIT 限制输出
LIMIT 起点, 数量 // 含头的  
LIMIT (n-1) * 每页条数, 每页条数

### 子句的顺序
WHERE GROUP ORDER LIMIT // 筛选 合并 排序 限制

### 函数 COUNT MIN MAX AVG SUM

## 使用Navicat客户端备份恢复数据库
备份：右键要备份的数据库或表，选择转储SQL文件。  
恢复：备份的SQL文件不会帮你创建数据库，所以你需要自己创建数据库，然后右键数据库名。
