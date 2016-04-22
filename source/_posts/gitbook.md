---
title: gitbook
date: 2016-04-19 20:52:41
tags:
- gitbook
categories:
- 工具
photos:
- 
description: gitbook使用方法
---


* gitbook本地环境搭建，初始化、编译和启动本地服务
* 记录使用过程中遇到的问题

{% asset_img logo.jpg 图片来源：谷歌图片 %}

<!--more-->


## 本地环境搭建

### 一、安装

* 执行`sudo npm install -g gitbook-cli`

* 执行`gitbook -V` ，如果返回gitbook的版本号则表示安装成功

### 二、初始化

* 执行`gitbook init`以初始化gitbook

### 三、编译
* 执行`gitbook build`后，会在根目录下生成_book文件夹，里面的index.html即为gitbook入口，可在浏览器中直接访问

### 四、启动本地服务
* 执行`gitbook serve`
* 启动gitbook服务器后会提示访问地址，如 http://localhost:4000 ，这样就可以在本地访问了
* 优点：修改文件后浏览器中立即会自动刷新，更新内容

## 在线开源电子书

### [gitbook.com](https://www.gitbook.com)

注册账号，关联github

## 使用注意

### 文件不要命名为index.md
* 问题
  * markdown文件避免命名为index.md，否则会导致访问该文件所在目录的README.md文件时始终会跳转到该index.md文件
* 原因
    * gitbook会将README.md编译成index.html，这就导致访问README.md会跳转到你命名为index.md的那个文件
