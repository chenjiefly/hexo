---
title: HTML相关知识
tags:
  - HTML
categories:
  - 技术
photos:
  - 
mathjax: false
description: HTML相关知识
date: 2016-06-27 11:18:12
---

* HTML标准
* HTML语义化

{% asset_img logo.jpg 图片来源：bing %}

<!--more-->

## HTML标准


## 标签语义化

### 一、意义
1.在没有CSS的情况下也能呈现较好的内容结构与代码结构
2.方便其他设备的解析，屏幕阅读器（如果访客有视障）会完全根据标记来“读”网页
3.有利于SEO，和搜索引擎建立良好沟通，有助于爬虫抓取更多的有效信息，比如h1~h6、strong用于不同权重的标题
4.便于团队开发和维护

### 二、渐进增强和优雅降级

1、渐进增强（progressive enhancement）

* 一开始只构建站点的最少特性，然后不断针对各浏览器追加功能

2、优雅降级（graceful degradation）

* 一开始就构建站点的完整功能，然后针对浏览器测试和修复

### 三、相似标签区别

1、strong和em

* strong是逻辑标签，包含语义为强调，表现形式可以为加粗、下划线、加大和标红等，有利于SEO
* em为物理标签，强调一种物理行为，仅仅是告诉浏览器将文字加粗

## HTML5