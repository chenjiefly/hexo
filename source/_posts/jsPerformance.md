---
title: Javascript 性能分析和提升技巧
tags:
  - JS性能
categories:
  - Javascript
photos:
  - 
mathjax: false
description: 记录Javascript的性能分析方法和提升性能的一些技巧
date: 2016-06-23 15:07:37
---

* 记录Javascript的性能分析方法
* 总结提升性能的一些技巧和方案

{% asset_img logo.png 图片来源：google %}

<!--more-->

## 频繁调用方法的执行控制
* 问题描述
    * 有些DOM事件是会频繁触发的，比如mouseover、scroll、resize和input等
    * 如果每次事件触发操作后都要进行较为复杂的运算，那么页面性能一定会急剧下降，直接导致页面卡顿

* 解决方案
    * 目前针对不同的应用场景有两种解决方法，即debounce和throttle

### debounce（去抖）
* 目的
    * *将一个连续的调用归为一个*
    * 希望频繁事件的回调函数在某段连续时间内，在事件触发后只执行一次
    * 注意*是在连续触发结束后的最后一次事件触发后的若干事件后执行一次回调*

* 场景
    * 以文本框输入为例，用户连续输入多个字符停止前，不应该去执行校验回调方法
    * 当用户停止键入后，如果在Xms内没有再触发输入事件，则执行一次回调方法

* 代码

```javascript
/**
 *
 * @param fn {Function}   实际要执行的函数
 * @param delay {Number}  延迟时间，单位是毫秒（ms）
 *
 * @return {Function}     返回一个“防反跳”了的函数
 */

function debounce(fn, delay) {

  // 定时器，用来 setTimeout
  var timer

  // 返回一个函数，这个函数会在一个时间区间结束后的 delay 毫秒时执行 fn 函数
  return function () {

    // 保存函数调用时的上下文和参数，传递给 fn
    var context = this
    var args = arguments

    // 每次这个返回的函数被调用，就清除定时器，以保证不执行 fn
    clearTimeout(timer)

    // 当返回的函数被最后一次调用后（也就是用户停止了某个连续的操作）
    // 再过 delay 毫秒就执行 fn
    timer = setTimeout(function () {
      fn.apply(context, args)
    }, delay)
  }
}
```
### throttle（节流器）
* 目的
    * *不允许方法在每Xms间执行超过一次*
    * 和debounce的主要区别是throttle保证方法每Xms有规律的执行

* 场景
    * 页面懒加载区块，需要边向下滚动边加载数据，而不是用户停止滚动后才去加载（*这与debounce有本质区别*）

* 代码

```javascript
/**
*
* @param fn {Function}   实际要执行的函数
* @param delay {Number}  执行间隔，单位是毫秒（ms）
*
* @return {Function}     返回一个“节流”函数
*/

function throttle(fn, threshhold) {

  // 记录上次执行的时间
  var last

  // 定时器
  var timer

  // 默认间隔为 250ms
  threshhold || (threshhold = 250)

  // 返回的函数，每过 threshhold 毫秒就执行一次 fn 函数
  return function () {

    // 保存函数调用时的上下文和参数，传递给 fn
    var context = this
    var args = arguments

    var now = +new Date()

    // 如果距离上次执行 fn 函数的时间小于 threshhold，那么就放弃执行 fn，并重新计时
    if (last && now < last + threshhold) {
      clearTimeout(timer)

      // 保证在当前时间区间结束后，再执行一次 fn
      timer = setTimeout(function () {
        last = now
        fn.apply(context, args)
      }, threshhold)

    // 在时间区间的最开始和到达指定间隔的时候执行一次 fn
    } else {
      last = now
      fn.apply(context, args)
    }
  }
}
```