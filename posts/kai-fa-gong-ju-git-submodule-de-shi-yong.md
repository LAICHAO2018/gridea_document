---
title: '[开发工具] Git Submodule的使用'
date: 2020-11-05 21:19:17
tags: [技术]
published: true
hideInList: false
feature: /post-images/kai-fa-gong-ju-git-submodule-de-shi-yong.jpg
isTop: false
---
## 1. 介绍
+ submodule子模块，简单来讲就是Git仓库中的子仓库。
+ 如果有一个模块是通用的，多个项目依赖这个模块；或者Github上要使用一个开源算法模块。但模块更新后，要怎么在自己的项目中保持同步更新甚至是提交呢？答案是使用Git Submodule。
## 2. 用法
### **2.1. 新增submodule**
```cpp
git submodule add $giturl $foldername
```
其中<font color=red>＄giturl</font>表示git仓库地址，<font color=red>＄foldername</font>表示submodule的目录名，例如：
```
git submodule add git://github.com/xx/A.git A
```

> 注：下面示例全部以 A 当做submodule所在目录

完成后，将文件变化提交即可
### **2.2. clone有submodule的git仓库**
```
git clone $giturl
git submodule init
git submodule update
```

### **2.3. submodule A作者有更新，如何同步到我的项目？**
**2.3.1 更新submodule A**
```cpp
 cd A
 git pull
```
**2.3.2 回到主git仓库，查看状态，并提交**
```cpp
 cd ..
 git status

 //输出结果有这样的内容，意思是submodule A有修改。add并提交即可
 modified:   A (new commits)

 git add .
 git commit -m 'update submodule'
 git push
```
**2.3.3 你的同事或者协作的开发者，如何更新？**
```cpp
 git pull
 git submodule update
```

这些操作和status的内容一开始可能很难理解，这里讲一下关于submodule的设计理念：
+ 主git仓库中存在<font color=red>.gitmodules</font>文件，它记录了submodule的基本信息。例如remote地址。
+ 同时在某处记录了主git仓库所用的submodule的commit号。
+ 主git仓库并不同步submodule中的所有代码，而是同步其remote地址和commit号，每个clone都是根据这两个信息自行到remote地址获取到该commit版本的内容。

所以，如果你要更新submodule必须做上面的操作步骤。而你操作完成后，你的git仓库中submodule的commit号得到更新。这样，与你协作的开发者，就可以直接<font color=red>git pull</font>得到最新的submodule commit号，<font color=red>git submodule update</font>获取submodule该commit的代码。

### **2.4. 主git仓库的开发者，同时也是submodule A的作者，如何在主git仓库修改A并同步？** 
如果理解了上面说的设计理念，那么这个操作非常简单。
1. 修改A目录中的内容
2. 提交并同步A
```cpp
 cd A
 git add .
 git commit
 git push
```
3. 此时，就处于3.2的状态，按照3.2操作即可。

### **2.5. 几个submodule都想更到最新**
简便操作:
```cpp
git submodule foreach git pull
```
这样所有submodule都更到最新了，add commit即可。

## 3.原文链接
+ [https://www.jianshu.com/p/384c73fe173f](https://www.jianshu.com/p/384c73fe173f)