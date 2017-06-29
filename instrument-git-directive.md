---
title: 常用git命令
tags: ["git"]
date: 2017-04-22 08:02:18
categories:
- 工具
- git
---
> 听说git可能是目前世界上最好的代码管理工具，下面整理下我迄今为止用到的git命令。不全，但极为实用...

<!-- more -->

### Git的几个概念
- Workspace 工作区

- Index/Stage 暂存区

- Repository 本地仓储

- Remote 远程仓储

### 我最常用的git命令
- 创建本地仓储
  ```bash
    git init
  ```

- 添加所有文件到git的跟踪列表（除去被.gitignore过滤掉的文件）
  ```bash
    // 或者git add .
    git add --all
  ```

- 提交代码到本地仓储
  ```bash
    git commit -m '提交日志'
  ```

- 将本地仓储与远程仓储建立联系
  ```bash
    // origin后面跟的就是你的远程仓储地址
    git remote add origin https://github.com/yesixuan/uhealth.git
  ```

- 将本地代码同步到远程仓储
  ```bash
    git push -u origin master
  ```

- 将远程仓储的代码拉到本地
  ```bash
    // 后面可以指定你想要存放的文件夹名
    git clone https://github.com/yesixuan/uhealth.git
  ```

- 将远程仓储的代码同步到本地
  ```bash
    git pull origin master
  ```

### 分支操作
- 创建并且切换到该分支
```bash
  git checkout -b [branch-name]
```

- 切换至主分支
```bash
  git checkout master
```

- 删除某分支
```bash
  git branch -d [branch-name]
```

- 提交分支到远程仓库
```bash
  git push -u origin [branch-name]
```

- 合并分支
```bash
  // 一旦出现冲突，需要手动解决冲突。解决完后，还要add一下
  git merge [branch-name]
```

### 其他重要的git命令
- 查看本地仓储的状态
  ```bash
    git status
  ```

- 对比代码的差异
  ```bash
    git diff
  ```

- 查看代码提交的日志
  ```bash
    git log
  ```

- 恢复到之前的某个版本
  ```bash
    // 哈希值可以在log中看
    git reset --hash 哈希值的前六位
  ```

- 下载远程仓储的所有变动
  ```bash
    git fetch [remote]
  ```

- 同步本地代码到远程仓储的指定分支
  ```bash
    git pull [remote] [branch]
  ```

- 同步所有分支到远程仓储
  ```bash
    git push [remote] --all
  ```
