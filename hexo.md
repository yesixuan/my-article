---
title: 我是用hexo搭建个人博客的经历
date: 2017-04-19 13:45:01
categories:
- 杂项
tags:
- hexo
- nextT
- blog
- 静态博客
---
## 首先贴出两个网址
[hexo的使用教程](http://www.jianshu.com/p/465830080ea9)
[nextT主题的替换教程](http://www.tuicool.com/articles/zeIZJzv)


<!-- more -->
<br>
## 下面总结下我自己的填坑经历

### 关于hexo
#### 本地操作
1. npm install -g hexo
2. hexo init
3. hexo generate（hexo g也可以，用来生成静态页面）
4. hexo server （浏览器输入http://localhost:4000）（然后去配置github，再继续第五步）
5. npm install hexo-deployer-git --save （这一步是在配置完github之后）
6. hexo deploy
#### github配置
1. 建立Repository （名字为yesixuan.github.io）
2. 修改项目配置文件 （根目录下的_config.yml）
```bash
deploy:
  type: git
  repo: https://github.com/leopardpan/leopardpan.github.io.git
  branch: master
```
#### 部署以及常用操作
1. hexo clean
2. hexo generate
3. hexo deploy
****
1. hexo new"postName" #新建文章
2. hexo new page"pageName" #新建页面
3. hexo generate #生成静态页面至public目录
4. hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
5. hexo deploy #将.deploy目录部署到GitHub
6. hexo help # 查看帮助
7. hexo version #查看Hexo的版本


### 关于更换主题（这里以nextT为例）
1. 下载主题的zip包。（地址：https://github.com/iissnan/hexo-theme-next/releases）
2. 结业后的文件夹改名为next，放入项目的themes目录里面
3. 打开站点配置文件，找到theme字段，并将其值更改为next 。
4. 此时可已在本地测试看看
5. 切换hexo里面的不同主题，在主题配置文件里面找到scheme的配置项，自己加减注释来切换


### 关于标签与分类 [原地址](https://segmentfault.com/q/1010000002561642)
1. hexo new page tags （hexo new page categories）
2. 确认站点配置文件里有tag_dir: tags （category_dir: categories）
3. 确认主题配置文件里有tags: /tags （categories: /categories）
4. 编辑站点的source/tags/index.md （略...），添加
```html
title: tags
date: 2015-10-20 06:49:50
type: "tags"
comments: false
```
