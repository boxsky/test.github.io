---
title: git remote之修改远程仓库
date: 2019/8/22 16:50:20
tags:
- git
categories:
- git
---

#### 由于公司的代码仓库迁移,所以代码仓库的remote地址发生了改变,此时需要更改remote地址，这样才可以切换到新仓库，我这里用的是方式1,也可以用方式2。

#### 方式1、直接修改:
```git
git remote set-url origin xxxxx.git
```
#### 方式2、先删后加:
```git
git remote rm origin
git remote add origin xxxxx.git
```



