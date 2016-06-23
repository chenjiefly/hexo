---
title: CSS知识小记
tags:
  - CSS
categories:
  - CSS
photos:
  - 
mathjax: false
description: 记录了CSS相关的知识点、技巧和常见问题等
date: 2016-06-23 16:49:05
---

* 记录了CSS相关的知识点、技巧和常见问题等

{% asset_img logo.jpg 图片来源：google %}

<!--more-->

## 盒模型

### 标准盒模型和怪异盒模型

{% asset_img boxModel.png 图片来源：MDN %}

1、标准盒模型

* 标准盒模型在pc端比较常用，因为pc端的屏幕大，有优势可以撑

```
盒子总宽度/高度 = width/height + padding + border + margin
```

2、怪异盒模型

* 怪异盒模型在手机端比较常用，因为手机屏幕是有限大小的，有时需要使用怪异盒模型把它固定住

```
盒子总宽度/高度 = width/height + margin = 内容区宽度/高度 + padding + border + margin
```

3、使用`box-sizing`决定盒模型类型

* CSS3属性`box-sizing`可以决定采用哪种盒模型，它有两个值一个是`content-box`，另一个是`border-box`
* 当设置为`box-sizing:content-box`时
    * 将采用标准模式解析计算，也是默认模式
    * `width`, `min-width`, `max-width`, `height`, `min-height` 与 `max-height` 控制内容大小
* 当设置为`box-sizing:border-box`时，将采用怪异模式解析计算


### margin 外边距叠加
1、现象描述

* 当两个或更多个垂直边距相遇时，它们将形成一个外边距
* 这个外边距的高度等于两个发生叠加的外边距的高度中的较大者
* 只有普通文档流中块框的垂直外边距才会发生外边距叠加
* 行内框、浮动框或绝对定位框之间的外边距不会叠加

2、三种发生情况

* 元素自身叠加
    * 当元素没有内容（即空元素）、内边距、边框时，它的上下边距就相遇了，即会产生叠加（垂直方向）
    * 当为元素添加内容、内边距、边框任何一项，就会取消叠加
* 相邻元素叠加
    * 相邻的两个元素，如果它们的上下边距相遇，即会产生叠加
* 包含（父子）元素叠加
    * 包含元素的外边距隔着父元素的内边距和边框，当这两项都不存在的时候，父子元素垂直外边距相邻，产生叠加
    * 添加任何一项即会取消叠加