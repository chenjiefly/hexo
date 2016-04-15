---
title: 搭建hexo + github 个人博客
date: 2016-04-14 13:44:29
tags: 
- hexo
categories:
- 博客
---

## 本地搭建hexo个人博客

### 官网

官网地址：[https://hexo.io/](https://hexo.io/)

### 本地环境
1、安装基本环境
* 安装git、node.js

* 安装hexo
```
npm install -g hexo
```

2、创建博客项目
* 使用hexo提供的脚手架创建
```
hexo init
```

* package.json
```json
{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "hexo": {
    "version": "3.2.0"
  },
  "dependencies": {
    "hexo": "^3.2.0",
    "hexo-deployer-git": "^0.1.0",
    "hexo-generator-archive": "^0.1.4",
    "hexo-generator-category": "^0.1.3",
    "hexo-generator-index": "^0.2.0",
    "hexo-generator-tag": "^0.2.0",
    "hexo-renderer-ejs": "^0.2.0",
    "hexo-renderer-marked": "^0.2.10",
    "hexo-renderer-stylus": "^0.3.1",
    "hexo-server": "^0.2.0"
  }
}
```

* 安装依赖包
```
npm install
```

* 本地启动博客服务
```
hexo server  
hexo s  # 简写命令
```
  打开[http://localhost:4000/](http://localhost:4000/)即可观察到博客欢迎页面


3、撰写博客
* 新建文章
```
# 执行下面命令可以在source/_posts下新建文章
hexo new "文章名"
hexo n  # 简写命令
```

4、生成静态网页
* 清除缓存后执行生成静态资源命令
```
# 执行下面的命令会在public目录下生成一系列html、css和js等文件
hexo clean  # 先清除缓存
hexo generate
hexo g  # 简写命令
```

5、部署到github
* 配置_config.yml中的github相关信息
```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy: 
  type: git
  repo: git@github.com:chenjiefly/chenjiefly.github.io.git
  branch: master
```

* 执行部署命令
```
hexo deploy
hexo d  # 简写命令
```

## github.io


## hexo 主题

### 官方主题集

主题集地址：[https://github.com/hexojs/hexo/wiki/Themes](https://github.com/hexojs/hexo/wiki/Themes)


