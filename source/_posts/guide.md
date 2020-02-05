---
title: 多终端更新本博客向导
date: 2020-02-05 14:55:47
tags:
---

## 前言

本博客页面使用 Hexo 框架搭建，一切操作都基于 Hexo 。

在操作之前需要确保环境（Node.js、Git、Hexo、hexo-deployer-git）已经配置完毕。

## 配置方法

首先将博客必要文件 pull 到本地（注意 clone 的 branch 是 hexo 而不是 master）

```
git clone git@github.com:SCU-SCST-SAST-MR/SCU-SCST-SAST-MR.github.io.git -b hexo
```

然后进入 clone 下来的文件夹

```
cd SCU-SCST-SAST-MR.github.io
```

最后安装必要的组件

```
npm install
```

## 更新方法

首先同步仓库文件

```
git pull origin hexo
```

然后按常规操作更新博客

```
# hexo n "blog-name"
```

然后将更新后的文件进行同步

```
git add source/
git commit -m "update blog by xx"
git push origin hexo
```

最后部署博客即可

```
hexo g -d
```

