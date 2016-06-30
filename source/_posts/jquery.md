---
title: jquery
tags:
  - jquery
categories:
  - Javascript
photos:
  - 
mathjax: false
description: jquery源码分析和使用技巧等
date: 2016-06-27 16:01:03
---

* jquery源码分析
* jquery使用技巧

{% asset_img logo.jpg 图片来源：bing %}

<!--more-->

## jquery源码分析

### CDN代码
[jquery-2.0.3.js](https://code.jquery.com/jquery-2.0.3.js)


------


### 整体框架提炼

```Javascript
(function( window, undefined ) {
    (  21,   94) 定义了一些变量和方法，jQuery = function() {}
    (  96,  283) 给jQuery对象，添加一些方法和属性
    ( 285,  347) extend : jQuery的继承方法
    ( 349,  817) jQuery.extend() : 扩展一些工具方法
    ( 877, 2856) Sizzle : 复杂选择器的实现，是独立模块，如果其他应用开发中需要用到选择器，可单独下载这个模块
    (2880, 3042) Callbacks : 回调对象，对函数的统一管理
    (3043, 3183) Deferred : 延迟对象，对异步操作的统一管理
    (3184, 3295) Support : 功能检测，如判断新老版本浏览器
    (3308, 3652) data() : data方法实现，数据缓存
    (3653, 3797) queue() : 队列管理，如多步骤动画，每一步都要先入队，执行一步出对一步
    (3803, 4299) attr() prop() val() addClass()等 : 对元素属性操作的相关方法
    (4300, 5128) on() trigger() : 对事件操作的相关方法
    (5140, 6057) DOM操作的相关方法 : 添加、删除、获取、包装、筛选和插入等
    (6058, 6620) css() : 对样式的操作，会考虑兼容性、属性值运算和容错等相关处理
    (6621, 7854) 提交数据和ajax : ajax() load() getJson()等
    (7855, 8584) 动画相关方法 : animate() show() hide() fadeIn() fadeOut()等
    (8585, 8792) 位置与尺寸相关方法：offset() scrollTop
    (8804, 8821) jQuery支持模块化的模式
    (8825, 8827) 接口暴露 : window.jQuery = window.$ = jQuery
})( window )
```

------

### jquery源码注释中的`#hash`

* jquery源码注释中有很多hash值，可以用来在线查询bug的更详细信息
* 以下网址可以搜索相关信息
    * [https://bugs.jquery.com/report](https://bugs.jquery.com/report)


------


### 使用闭包传递`window`的原因

* 闭包内的变量是局部的，利于和外部模块隔离
* 将`window`对象作为参数传入闭包
    * 引用速度快
    * 压缩版本中，只需要形参为`window`，闭包内的`window`可以全部压缩为单字母`e`
* 传入`undefined`原因
    * `undefined`是可以被修改的，如`undefined = 10`
    * 之所以这样传入`undefined`是为了防止`undefined`在外部被修改

------

### (21, 94) 定义的一些变量和方法，jQuery = function() {}
1、rootjQuery

* document对象的jquery对象，赋值后的变量便于代码压缩
* `rootjQuery = jQuery(document);`

2、core_strundefined = typeof undefined

* 将`undefined`转换为字符串类型，便于后续的变量比较
* 之所以用字符串是因为兼容性问题，如IE6-IE9时，xmlNode节点的方法可能无法判断是否为`undefined`
* 而字符串的`undefined`是全部兼容的

3、`_jQuery`和`_$`

* 防冲突使用的两个变量
* 如果`jquery`外面的代码已经占用了`window`下的`jQuery`和`$`，则在这两个变量里缓存，以防止冲突

4、`class2type = {}`

* `$.type()`方法中判断每个元素的类型
* 生成后的参考内容`class2type = { '[Object String]': 'string', '[Object Array': 'array'}`

5、**jQuery对象定义**

* 源码提炼

```Javascript
/* 源码61行 */
// Define a local copy of jQuery
jQuery = function( selector, context ) {
    // The jQuery object is actually just the init constructor 'enhanced'
    return new jQuery.fn.init( selector, context, rootjQuery );
}

/* 源码96行 */
jQuery.fn = jQuery.prototype = {
    ...
}

/* 源码283行 */
// Give the init function the jQuery prototype for later instantiation
jQuery.fn.init.prototype = jQuery.fn;
```

* 这样定义是为了链式操作`jQuery().css()`，每一个方法都需要返回一个`jquery`对象
* 如果使用下面代码实现类似功能，则会导致内存泄露，最终浏览器崩溃

```
// 很明显递归调用了，无限地new肯定会导致内存爆掉
jQuery = function( selector, context ) {
    return new jQuery( selector, context, rootjQuery );
}
```

* `jQuery`和`jQuery.fn.init`这两个方法的地址是不同的，但是原型是相同的
* 之所以jQuery方法返回的对象拥有所有的jQuery方法，就是因为原型对象的传递赋值
    * 上面后两段代码合并结果就是`jQuery.fn.init.prototype = jQuery.prototype`
    * 所以`jQuery.fn.init`返回的对象和`jQuery`拥有相同的方法


------

### (96, 283) 给jQuery对象，添加一些方法和属性
1、对象和方法的简化

```Javascript
jQuery.fn = jQuery.prototype = {
    jquery: 版本号
    constructor: 修正构造器的指向问题，因为jQuery.prototype = {...}被对象给覆盖了，构造器就被修改为Object了
    init(): 初始化和参数管理
    selector: 存储选择字符串
    length: this对象的长度
    toArray(): 类数组对象转换为数组，$('div'): {0: div, 1: div, 2: div, length: 3} => [div, div, div]
    get(): 转换为原生元素集合，写下标得到下标对应的原生元素，不写下标参数则返回所有原生元素的集合
    pushStack(): jQuery对象入栈，$('div').pushStack($('span')).css()，实际是对span设置样式
    each(): 集合遍历
    ready(): DOM加载成功后执行方法fn，fn为入参方法，jQuery.ready.promise().done( fn );
    slice(): 截取jquery对象集合中的元素，类似数组的slice
    first(): 获取jquery对象集合中的第一个元素
    last():获取jquery对象集合中的最后一个元素
    eq(): 获取jquery对象集合中的第n个元素，n为方法入参
    map(): 
    end(): 返回pushStack栈中的前一个元素

    // 数组的三个方法，源码内部使用
    push(): 数组元素入栈
    sort(): 数组排序
    splice(): 数组截取、删除和添加方法
}
```

------


### (285, 347) extend : jQuery的继承方法

1、扩展功能的方法extend实现

* jQuery.extend = jQuery.fn.extend = function() {}
    * 工具和插件扩展方法extend的具体实现
    * 使用jQuery.extend扩展的是工具方法
    * 使用jQuery.fn.extend扩展的是对象插件

* 当写一个对象字面量时，实际是扩展工具或对象插件

```
// 扩展jquery插件工具方法，调用方法为：$.aaa()和$.bbb()
// $.extend();  ->  this  -> $  -> this.aaa  -> $.aaa()
$.extend({
    aaa: function() {
        ...
    },
    bbb: function() {
        ...
    }
});

// 扩展jquery对象实例方法，调用方法为：$().aaa()和$().bbb()
// $.fn.extend();  ->  this  -> $.fn  ->  $.prototype  -> this.aaa  -> $().aaa()
$.fn.extend({
    aaa: function() {
        ...
    },
    bbb: function() {
        ...
    }
});
```


* 当写多个对象字面量时，实际是对象拷贝操作，后面的对象全都扩展到第一个对象上

```
var a = {};
$.extend({}, {name: 'hello'}, {age: '20'});
```

2、浅拷贝和深拷贝

```
// 默认浅拷贝
$.extend({}, {name: 'hello'}, {age: '20'});

// 深拷贝
$.extend(true, {}, {name: 'hello'}, {age: '20'});
```

3、继承的方式

* jQuery中的拷贝继承
    * 将目标对象中没有的属性从源对象中拷贝到目标对象上
* JS中的类式继承
    * 针对的是new 构造函数
* JS中的原型继承
    * 利用prototype来继承

### (349, 817) jQuery.extend() : 扩展一些工具方法

1、expando

* 每一个jquery对象在页面中的唯一标识
* 主要作用是作为映射关系的键

2、noConflict()

* 防冲突方法，防止加载jquery.js库前，$和jQuery就已经被占用

```
var jq = $.noConflict();  // 放弃$
var JQ = $.noConflict(true);  // 放弃jQuery
```

3、isReady

* window.onload = function() {}
    * 需要等DOM和资源文件都加载完成才执行JS
    * 如`<img src=""/>

* $(function() {})
    * 只要DOM加载完就执行JS
    * 不需要等图片加载完成，速度比window.onload快
    * `$(function() {})  =>  $(document).ready()  =>  $().ready()  => $.fn.ready()  => jQuery.ready.promise().done( fn )  =>  $.ready()`

4、readyWait

* 推迟DOM ready的变量锁
* 可能会为了等很多文件，所以该变量锁是一个计数器

5、holdReady()

* DOM 推迟 ready

* 写法

```
$.holdReady(true)   // 推迟一次，jQuery.readyWait++
$.holdReady(false)  // 释放推迟一次，--jQuery.readyWait
```

* 作用：异步加载文件要先执行，然后才执行DOM加载完成后的方法

6、ready()

* DOM ready的三种写法

```
$(function() {});
$(document).ready(function() {});
$(document).on('ready', function() {});
```

7、简单工具方法

* isFunction()：判断是否为函数
    * 低版本IE浏览器下，alert等原生函数返回的结果是`Object`，可参考bug#2968
* isArray()：判断是否为数组
    * jquery@2.0.3不支持IE6、7、8，所以该方法直接用原生的，速度快
* isWindow()：判断是否为window对象
    * obj != null 的作用是为了防止假值之间的判断，因为undefined == null为真
    * obj === obj.window 的判断实现，因为只有window对象下才会有window
* isNumeric()：判断是否为数字
* isPlainObject()： 判断是否为对象字面量
    * DOM节点和`window`排除在对象字面量外
    * 系统自带的对象，如`window.location`要排除在外，利用只有对象字面量的`constructor.prototype`才会有`isPrototypeOf`真这个原理
* isEmptyObject(): 空对象判断
* error(): 抛出异常

8、parseHTML()

* keepScripts
    * 作用是如果HTML片段中有script标签，那么如果该值为true，则script标签中的代码会被执行
    