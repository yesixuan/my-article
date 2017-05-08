---
title: npm
tags: ["npm"]
date: 2017-05-08 16:10:00
categories:
- 开发工具
- npm
---
> 本文旨在拾起一些实用但是容易被忽略的npm小知识...

<!-- more -->
### 安装制定版本的包
```bash
  $ npm install <packageName>@1.7.13
```
### 更新包
```bash
  $ npm update <packageName>
```
### 清空本地的缓存包
```bash
  $ npm cache clean
```
### 从本地缓存中现在包
```bash
  # 意味着9999999分钟之后才上网下载包
  $ npm install --cache-min 9999999 <package-name>
```
### 针对国内的特殊网络环境
```JS
  // 修改node安装目录的npmrc配置文件，添加如下代码
  registry = https://registry.npm.taobao.org
  sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
```
