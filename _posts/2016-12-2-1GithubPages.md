---
layout: post
title:1.【折腾】从记录github-pages建设开始
---


### 写在前边的废话：

感觉该写点东西记录下每天的成长了，最终选择了这个，一是支持md，二是免费稳定，三是版本控制刚好学习一下git，好吧貌似逼格高点？

首先贴出今天最重要的一段话

#### 建站两种方式

- username.github.io的项目， 在master分支下存放网站源代码，访问username.github.io直接访问，跟我这一样
- 建立名为 project 的项目，在 gh-pages分支下存放网站源代码，访问username.github.io/project访问

这块浪费了几个小时的时间，不少文章好像没写明白。

#### 期间遇到的问题

- ssh rsa密钥有问题，进.ssh/清know_hosts ，清已有rsa

## 主流程简单记录

- ssh-keygen -t rsa -C zenppl@163.com 
- rsa存储位置不需要改
- 密码设置
- 将.ssh/id_rsa.pub 记录到github-->settings-->ssh keys中
- ssh -T git@github.com  (-v详情)
- 出success ，完事

## jekyll建站

- fork一份模板
- setting-->改项目名称 zenppl.github.io
- git clone 到本地
- 修改
- git add .
- git commit -m "update"
- git push 

## 相关使用到的git命令

- git init 创建文件夹后初始化
- git pull 
- git push

- git remote add origin branchName 向远程添加分支
- git push origin branchName 

- git branch -r -d origin/branchName 删除分支
- git push origin :branchName 

ps:用惯了svn，git语法有点不习惯

滚回去继续打包了~注定又是一个不眠之夜

