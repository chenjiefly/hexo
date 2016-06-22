---
title: 前端JS调试技巧
tags:
  - Javascript
  - debug
categories:
  - Javascript
  - debug
photos:
  - 
mathjax: false
description: 归纳总结了一些前端JS调试技巧
date: 2016-06-22 17:21:09
---

* Console
* JS断点调试
    * Sources断点
    * debugger断点
    * DOM断点XHR断点
    * Event Listener断点

{% asset_img logo.jpg 图片来源：bing %}

<!--more-->

## JS常用调试技巧

### Console
1、方法API
参考：[https://developer.mozilla.org/zh-CN/docs/Web/API/Console](https://developer.mozilla.org/zh-CN/docs/Web/API/Console)

### JS断点调试
* 快速打开开发者面板
    * 快捷键`<command + option + i>`
* 快速切换开发者面板中的子面板
    * 向左切换快捷键`<command + [`
    * 向右切换快捷键`<command + ]`

#### Sources断点
* 就是在chrome的Sources面板中找到对应JS文件，并在某行代码上打断点
* 快速定位文件的技巧
    * 使用快捷键`<command + o>`

{% asset_img breakpoint-sources.gif %}

#### debugger断点
* 在代码中显示添加`debugger;`这条语句，就可以在浏览器中运行时自动在该代码处被中断
* 适用于HTML文件中的代码片段或无法准确定位需要调试语句的位置时

{% asset_img debugger.gif %}

#### DOM断点
* DOM断点
    * 顾名思义就是在DOM元素上添加断点，进而达到调试的目的。而在实际使用中断点的效果最终还是落地到JS逻辑之内

* 三种断点类型
    * 当节点内部子节点变化时断点（Break on subtree modifications）
    * 当节点属性发生变化时断点（Break on attributes modifications）
    * 当节点被移除时断点（Break on node removal）

* 添加方法
    * 直接在要加断点的DOM节点上右击鼠标
    * 在弹出菜单的“break on...”子菜单中选择即可

* 效果
    * 当DOM发生断点对应类型的事件时，会中断引起该结果的JS语句处

#### XHR断点

* “XHR Breakpoints”是专为异步而生的断点调试功能
* 可以通过“XHR Breakpoints”右侧的“+”号为异步断点添加断点条件，当异步请求触发时的URL满足此条件，JS逻辑则会自动产生断点

{% asset_img breakpoint-xhr.gif %}

#### Event Listener断点

* 事件监听器断点，即根据事件名称进行断点设置。当事件被触发时，断点到事件绑定的位置
* 事件监听器断点，列出了所有页面及脚本事件，包括：鼠标、键盘、动画、定时器、XHR等等

{% asset_img breakpoint-event.gif %}



> 引用自[http://seejs.me/2016/03/27/jsdebugger](http://seejs.me/2016/03/27/jsdebugger/)