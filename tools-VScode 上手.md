---
title: VScode上手
tags: ["VScode"]
date: 2017-08-24 09:07:00
categories:
- 开发工具
---
> 从webstorm到sublime，再到atom。一路上兜兜转转，直到昨天晚上开始了与VScode的第一次邂逅...

<!-- more -->
## 渊源
还记得上手的第一个编辑器是webstorm，第一次知道代码还可以写得这么流畅。但是很快就觉得它太大了，让我的机器不堪重负。后来知道有一个叫做sublime的轻量级编辑器，各种配置都是通过代码（而不是点选操作）来控制，可以安装各种插件，感觉是高手装逼专用。然后兴致勃勃地捣鼓各种插件。  
sublime算是用的事件比较长的一款编辑器，但就像小两口一样，日子过久了自然就会出现各种小矛盾。本身底子有点薄弱以及日益庞大的插件群便是我与sublime矛盾的根源了。  
与atom的交集是始于它的美貌，结束于它的空有其表（个人认为）。与webstorm差不多的体积，功能的集成程度却差得太远。没换电脑之前估计还hold不住它。  
对VScode的第一印象不算太好。首先它的默认界面就给人一种古板、老旧的感觉。其次这货是微软出的，印象就更差了。但VScode确实超出我的预期。开源免费、对中文友好、功能集成度高、运行流畅...值得称道的东西太多。  

## 使用技巧
### 控制面板
按`Ctrl + shift + p`或者`F1`打开控制面板。
### 修改默认快捷键
按`F1`打开控制面板，输入“keyboard shortcut file”，参照默认快捷键设置来自定义快捷键。
### 开启terminal
按`Ctrl + shift + oem_3(反引号)`，`Ctrl + k`清屏操作。
### markdown预览
界面右上角的有小图标，可以在右侧开启markdown预览。
### 拆分编辑器
右上角类似翻开书本的小图标可以拆分编辑器，`Ctrl + \`快捷键也是可以的。
### 关于git
VScode已经集成Git管理，这一点大赞。开启源代码管理界面，可以清楚地看到你所作的修改。提交代码只需要在消息框中输入提交日志，按`Ctrl + Enter`提交。  
左下角有图标显示本地与远程代码的状态，点击该图标就可以拉取或是提交代码到远端。  


## 必备插件
备注信息：*8d32546b11e0fa881ddee3928583e95b6c1e8221*

### 自动生成文档注释`vscode-fileheader`
1. 安装`vscode-fileheader`插件;
2. 在用户配置json文件中添加`"fileheader.Author": "yesixuan", "fileheader.LastModifiedBy": "xuan",`;  
3. 生成文档注释：`ctrl+alt+i`。  

### vscode-icons
话不多说，直接上插件`vscode-icons`。

### Debugger for Chrome
让vscode映射chrome的debug功能，静态页面都可以用vscode来打断点调试。（这个有待后续研究）

### VUE插件
vetur：语法高亮、智能感知、Emmet等；  
VueHelper：snippet代码片段。

### Syncing备份
Syncing插件用来备份编辑器所有配置数据（包括插件）。  
上传配置信息：打开控制面板，依次输入**upload**、**token**，Gist ID给个空的让其随机生成即可。  
下载配置信息：打开控制面板，依次输入**download**、**token**，选择一个Gist ID。

## 常用快捷键
- 移动行上下：Alt + up/down
- 删除行：Ctrl + Shift + K
- 缩进：Ctrl + ] / [ 或 Tab / Shift + Tab
- 替代鼠标滚轮：Ctrl + Up / Down
- 整页地翻：Alt + PageUp / PageDown
- 折叠展开区块代码：Ctrl + Shift + [ / ]
- 跳转文件：Ctrl + P
- 切换最近打开：Ctrl + Tab
- 多选 / 跳选：Ctrl + D / K
- 从光标处扩展选中全行：Shift + Alt + right
- 选择所有出现在当前选中的词汇-操作：Ctrl + F2
- 侧边栏显示隐藏：Ctrl + B
- 选中行（可以连续选）：Ctrl + I
