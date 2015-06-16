---
layout: post
title: git使用
description: "objective-c语法的一些特性，以及面向对象特性"
category: personal
tags: [git, 源代码管理工具, github]
imagefeature: 
comments: true
mathjax:
---

------

## git

### 配置git仓库

一次性的配置方式：会配置到被管理文件的。git文件夹下

	//告诉git，who you are?
	git config user.name xxxxx
	//告诉git，how connection you?
	git config user.email xxx@xxxx

一劳永逸的配置仓库环境：

	git config --global user.name xxxx
	git config --global user.email xxx@xxx
	
<!--more-->	
	
### git常用指令

	//将项目的整个仓库克隆到本地
	git clone 
	//查看文件状态
	git status
	//添加文件到"暂存区";PS:git 中每次创建或者修改之后都要重新add
	git add 文件名称/目录
	//添加文件到"本地仓库"
	git commit -m 文件名称 "描述"
	//查看log
	git log 文件名
	//查看所有的修改信息(所有版本)
	git reflog
	//返回到某一版本
	git reset --hard 版本号(只需要写前7位)
	//恢复成为仓库中代码(类似于svn revert)
	git checkout 文件/目录

### git注意事项

* git 的clone 会将项目的整个仓库克隆到本地;git 中的commit是提交到本地仓库
* push ：把本地仓库中的代码推送到共享代码库(即服务器代码库)
* pull ：从服务器上拉下来最新的代码(相当于update)
* git默认没有简写指令，且一般情况下不建议自定义简写指令，git中的简写称之为起别名
* git中的版本号是一个40位的哈希值(切换版本只需要输入前七位)，svn中的版本号是一个递增的整数；git是**分布式**的、svn是**集中式**的
* git是先提交到本地仓库，在提交到远程仓库，每台电脑都有一个仓库
* git可以多个人一块儿改storyboard，但是svn不可以
* 

### git分支管理

	//在本地代码库给项目打上一个标签
	//注意： 此时此刻打上的这个标签仅仅是一个本地标签。（和服务器没有关系）
	git tag -a v1.0 -m 'Version 1.0’
	//查看当前标签
	git tag
	//将标签添推送到远程代码库中
	git push origin v1.0
	
	//从服务器下载最新代码,利用git checkout v1.0指令快速切换到1.0版本
	//根据提示：开启一个新的分支开始修复代 git checkout -b 1.0bug_fix
	
	//合并修复后的代码到主线
	

### 公司远程仓库使用

1. 新建git远程仓库:  git init -bare
2. 项目经理初始化项目
	>* 先克隆一份空的仓库到本地
	>* **忽略不需要加入版本控制器的文件以及文件夹** 配置忽略文件只需要到github上搜索.gitignore拷贝别人写好的代码即可
配置.gitignore一定要在和.git隐藏文件夹同一级的目录下
>* 生成好.gitignore文件之后， 还需要将.gitignore文件添加到版本控制 add commit
>* 新建项目，先将代码提交到本地仓库，再将代码提交到远程仓库 *git中默认就会创建一个分支， 这个分支叫做origin/master， 相当于svn中的trunk*
	
### 通过Xcode将代码提交到github上

1. 注册一个github账号
2. 在Setting中配置添加一个SSH keys(PS:根据github页面上的generating SSH keys文档)
3. 完成后，打开github主页，点击仓库(Repositories)新建一个(PS:可以直接设置忽略文件，以及license选择：Apache)
4. 在本地clone仓库，并在仓库中创建xcode项目(以后就可以在xcode中提交代码到github)
5. PS：在xcode中提交时，如要求输入用户名密码时，用户名应该填github的昵称
