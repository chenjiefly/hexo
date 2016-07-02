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


------


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
    * 如`<img src=""/>`

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
* parseJSON(): JSON.parse()
* each(): 遍历集合，数组、对象、类数组对象
* trim(): 去除字符串前后空格
* inArray(): 判断入参元素是否存在于数组中，数组版的indexOf
* map(): 映射新数组
* now(): 获取当前时间，即Date.now()方法

8、parseHTML()

* keepScripts
    * 作用是如果HTML片段中有script标签，那么如果该值为true，则script标签中的代码会被执行
* 单标签`<li></li>`
    * 使用`context.createElement( parsed[1] )`生成节点
* 多标签   `<li></li><script></script>`
    * 使用`jQuery.buildFragment( [ data ], context, scripts )` 

9、parseXML()

* 解析XML格式数据

```
var tmp = new DOMParser(),  // IE8以下不支持
    xml = tmp.parseFromString( data , "text/xml" );  // data是XML对象字串
```

10、noop()

* 空函数，做插件和组件开发时可作为默认参数

```
function aaa() {
    this.default = {
        show: function() {}  // 可用$.noop代替
    }
}
```

11、globalEval()

* 全局解析JS，严格模式下采用创建`<script>`标签插入页面中的方法实现，一般模式下直接使用`eval`解析
* `eval`和`window.eval`的区别
    * `eval`是关键字，声明的变量局部有效，并不能为全局变量
    * `window.eval`是方法，可以解析为全局变量

12、camelCase()

* 转驼峰方法（内部使用），如margin-top转换为marginTop
* 注意`-ms-opacity`会先转为`ms-opacity`，再转换为`MsOpacity`

13、nodeName()

* 是否为指定节点名（内部使用）
* 名字对比时，需要转换为小写，因为在不同浏览器下节点名称可能大小写不统一

14、makeArray()

* 类数组转换为真数组（有第二个参数result，那就是内部使用的）
* 字符串、数字等都可以转成数组

15、merge()

* 合并数组

```
$.merge(['a', 'b'], ['c', 'd'])
```

* 合并特殊json对象

```
$.merge(['a', 'b'], {0: 'c', 1: 'd', length})
$.merge({0: 'c', 1: 'd', length: 2}, ['a', 'b'])
$.merge({0: 'a', 1: 'b', length: 2}, {0: 'c', 1: 'd', length})
```

16、grep()

* 过滤新数组
* `grep: function( elems, callback, inv ){...}`
    * elems为数组
    * callback为过滤方法，返回true或false
    * inv决定返回callback返回的值为真或假时对应的元素，相当于集合取反

17、guid

* jquery内部的唯一标识符，可以累加
* 与事件操作有关系，将事件操作和回调方法关联起来

18、proxy()

* jquery中改变方法内部this指向的方法

```
function show(x, y) {
    console.log(x);
    console.log(y);
}

// 返回的是一个方法，还不能执行
$.proxy(fn, context)

// 这样就能执行了
$.proxy(fn, context)()

// 传参数
$.proxy(fn, context, x0, y0)()  // 不触发方法时的传参
$.proxy(fn, context)(x0, y0)    // 直接执行方法时的传参

// 简化写法，改变对象内方法中的this指向
var obj = {
    show: function() {
        console.log(this);
    }
}

$(document).click($.proxy(obj.show, obj));  // 普通写法
$(document).click($.proxy(obj, 'show'));    // 简化写法

```

19、access()

* 多功能值操作（内部使用）
* 用于适应$().css()、$().attr()等get/set方法的公用方法
* 参数
    * elems : 元素集合
    * fn : 回调函数，用于css()、attr()这些方法的个性化处理
    * key : css()、attr()设置参数对象的key
    * value : key对应的值
    * chainable : 决定设置（true）还是获取（false）操作
    * emptyGet : 获取操作情况下，当匹配到的集合为空时，获取的值
    * raw : 传入的value是否为方法，true表示不是方法是对象

20、swap()

* 交换元素的style属性
* 场景
    * `$().width()`可以获取到设置了`display:none;`的元素的宽度
    * `$().get(0).offsetWidth`获取不到设置为`display:none;`的元素的宽度
    * jquery实现`width()`方法功能就是利用了`swap()`方法
* 原理
    * 先保存原来的`display:none;`存起来
    * 然后设置元素样式为`display:block;visibility: hidden;position: absolute;`
    * 于是就可以使用`$().get(0).offsetWidth`获取宽度
    * 最后将设置的样式恢复为原来保存的即可


------


### ( 877, 2856) Sizzle : 复杂选择器的实现，是独立模块，如果其他应用开发中需要用到选择器，可单独下载这个模块





------


### (2880, 3042) Callbacks : 回调对象，对函数的统一管理
1、功能

* 用于管理一系列的回调函数列表，在内部使用时为`$.ajax()`和`$.Deferred()`提供基本功能模块
* 用于可以实现统一触发一批不同作用域下的函数

2、方法

```
var cb = $.Callbacks();

cb.add(fn1);     // 相当于绑定事件
cb.add(fn2);
cb.fire();       // 相当于触发事件，一旦触发连续执行fn1和fn2
cb.remove(fn1);  // 相当于移除事件

```

3、 `$.Callbacks()`方法入参

* once
    * 
* memory

* unique

* stopOnFalse