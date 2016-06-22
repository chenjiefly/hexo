---
title: Canvas
tags:
  - Canvas
categories:
  - Javascript
photos:
  - 
mathjax: true
description: Canvas学习笔记
date: 2016-04-24 22:23:53
---

* Canvas学习笔记
* 参考书籍《HTML5 Canvas基础教程>
* 使用require.js组织代码模块
* github示例demo地址：[https://github.com/chenjiefly/Canvas](https://github.com/chenjiefly/Canvas)

{% asset_img logo.jpg 图片来源：baidu %}

<!--more-->

## Canvas学习笔记
### 一、学习教材
* HTML5 Canvas基础教程

### 二、工程内容
* 书中的例子
* github地址
    * [https://github.com/chenjiefly/Canvas](https://github.com/chenjiefly/Canvas)

### 三、文件组织
* 使用了require.js，尝试了不同模块（用或未用define定义）的引用

### 四、文档结构和内容
1、config.js文件为require.js的配置文件

2、main.js为总的入口文件

3、lib文件夹下存放库文件

4、comm文件夹下存放通用文件
* util.js
    * 清除画布，该操作会消除所有属性，如样式和线宽等
    * canvas窗口最大化
    * canvas随window变化而自动重绘

5、base文件夹下为canvas基础应用
* graph：矩形、直线、圆弧、圆的绘制，矩形区域的擦除
* text：文本的绘制
* property：相关属性设置（线宽、颜色、字体等）

6、advanced文件夹下为canvas的高级应用
* 保存和恢复绘图状态 save&recover
    * 绘图状态指的是描述某一时刻2D渲染上下文外观的整套属性，从简单的颜色值到复杂的变换矩阵及其他特性
    * 绘图状态的全部属性为：
        * 变换矩阵
        * 裁剪区域（clipping region）
        * global-Alpha
        * globalCompositeOperation
        * strokeStyle
        * fillStyle
        * lineWidth
        * lineCap
        * lineJoin
        * miterLimit
        * shadowOffsetX
        * shadowOffsetY
        * shadowBlur
        * shadoColor
        * font
        * textAlign
        * textBaseline
    * **画布上的当前路径和当前位图（正在显示的内容）并不属于状态**
    * 绘图状态是以**绘图状态栈**的形式保存的
    * context.save()：调用绘图上下文context的save方法可将状态入栈
    * context.restore()：调用绘图上下文context的restore方法可将状态出栈
    * 保存和恢复绘图状态其实就是入栈和出栈的过程，无论是单个还是多个绘图状态的保存和恢复都遵循后入先出原则

* 变形 transform
    * 平移
        * 平移的本质是移动画布原点，其默认原点坐标为(0, 0)，当设置为(-10, 60)时，原点向左平移10px，向下平移60px，与图形平移一致
    * 缩放
        * 缩放后可能会导致显示位置不正确，可以采取先平移再缩放的方式来解决
    * 旋转
        * 整个画布按照原点旋转，因此在旋转之后绘图会使图形显示位置偏移，需要校准显示位置
        * 要实现在原位置上对所绘图形进行旋转，需要将原点平移到所绘图形中心，然后做一次旋转，最后以原点为中心绘图，详见_showRotate演示方法
        * 正弧度表示顺时针方向旋转
    * 变换矩阵
        * 在画布中使用的是一个3x3的2D渲染上下文变换矩阵
        ```
         --      --      --                      --
        |  a  c  e |    |  x轴缩放  x轴倾斜  x轴平移  |
        |  b  d  f | == |  y轴倾斜  y轴缩放  y轴平移  |
        |  0  0  1 |    |    0       0       0     |    -- 这一行不能修改
         --      --      --                      --
        ```
        * 该矩阵默认值是3阶单位矩阵
        * context.transform(a, b, c, d, e, f)
        * **使用变换矩阵进行旋转是倾斜和缩放的组合效果**，参看例子，只需要一个角度值就可以旋转

* 合成 Combine
    * **修改全局合成属性会影响到修改之后所绘制的全部内容**
    * 全局阿尔法值globalAlpha，取值范围0.0（全透明）到1.0（不透明），默认1.0
    * 局部Alpha值会参照globalAlpha值（充当基数），具体表现为两者相乘后的透明度
    
    * 源：待绘制的图形
    * 目标：已经绘制到画布上的图形

    * source-over：默认值，源绘制在目标之上
    * destination-over：目标绘制在源之上
    * source-atop：源绘制在目标之上，未重合区域的源透明
    * destination-atop：目标绘制在源之上，未重合区域的目标透明
    * source-in：只绘制重合区域的源
    * destination-in：只绘制重合区域的目标
    * source-out：只绘制不重合区域的源
    * destination-out：只绘制不重合区域的目标
    * lighter：与顺序无关，重叠部分颜色相加，最大值为255（白色）
    * copy：与顺序无关，只绘制源，并覆盖目标
    * xor：与顺序无关，绘制不重叠的源和目标，重叠部分透明

* 阴影 Shadow
    * shadowBlur：模糊效果的边界宽度像素值
    * shadowOffsetX：水平方向模糊偏移量
    * shadowOffsetY：垂直方向模糊偏移量
    * shadowColor：模糊颜色，使用'rgb(r, g, b)'来设置

* 渐变 Gradient
    * fillStyle和strokeStyle都接受以CanvasGradient对象表示的渐变颜色值
    * 画布支持两种类型渐变：线性渐变和放射渐变，两种创建方法都返回一个CanvasGradient对象
    * 线性渐变
        * 创建方法：createLinearGradient(startX, startY, endX, endY)，参数为渐变线起止点坐标
        * 设置起止渐变颜色方法：addColorStop(offset, color)，offset为渐变状态（0~1），color为'rgb(r,g,b)'
    * 放射渐变
        * 创建方法：createRadialGradient(x0, y0, r0, x1, y1, r1)
        * **必须要理解径向渐变的原理**

* 复杂路径
    * 每次调用moveTo或lineTo都会给子路径增加一个相应的(x, y)坐标值，首尾相连的线段可组成图案
    * closePath会封闭路径，即连接起点和终点之间的路径

    * 贝塞尔曲线 bezier
        * quadraticCurveTo(cpx, cpy, x, y)：二次贝塞尔曲线，参数分别为控制点和目标点坐标
        * bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)：三次贝塞尔曲线，参数分别为两个控制点和目标点坐标

* 导出画布为图像
    * toDataURL方法用于画布导出为图像，返回的数据中前四个单词为"data:image/png;base64"，表示该数据是base64编码的PNG图片数据
    * 在base64后的所有字符和符号都是以文本表示的画布图像
    * 将该数据直接赋给`<img>`标签的src特性可以直接显示图片

7、img&vedio文件夹下为处理图像和视频
* 处理图像
    * 加载方法：context.drawImage(image, x, y)，image可以是img元素、canvas元素或video元素
    * 调整图像大小：drawImage(image, x, y, width, height)，width和height为图像尺寸大小
    * 裁剪图像：drawImage(image, sx, sy, sw, sh, dx, dy, dw, dh)，s打头的为裁剪区域左上角坐标和尺寸，d开头的为待绘制裁剪图形的左上角坐标和尺寸
    * **加载的图像似乎会跟着坐标原点移动**
    * 访问像素值
        * 注意可能会有错误提示“Failed to execute 'getImageData' on 'CanvasRenderingContext2D': The canvas has been tainted by cross-origin data.”，解决方案是HTML通过服务器访问
        * context.getImageData(x, y, width, heigth)
        * (x, y)为width*height区域的左上顶点的坐标
        * 返回一个2D渲染上下文的ImageData对象，该对象包含三个属性，宽、高和区域全部像素数组CanvasPixelArray
        * CanvasPixelArray是一个JS一维数组，每个像素用4个整数值表示，范围0~255，分别表示rgba
        * 精确访问数组中像素的公式
            * $pixelRed = (y - 1) × (width × 4) + (x - 1) × 4$
            * $pixelGreen = pixelRed + 1$
            * $pixelBlue  = pixelRed + 2$
            * $pixelAlpha = pixelRed + 3$
    * 创建像素值
        * 方法：createImageData(width, height, data)
    * 绘制图像
        * 方法：inputImageData(data, x, y)，x和y为相对于画布原点的坐标
        * 翻转颜色：255-当前颜色值
        * 灰度
            \begin{aligned} 
            grey=\frac{r + g + b}{3}
            \end{aligned}

    * 像素化
        * 取图像中对应点的颜色，并在该点上绘制该颜色的矩形、圆形等图案
* 处理HTML5视频
    * 创建HTML5 vedeo元素
        * 


7、制作动画
* 创建动画循环
    * 循环三要素
        * 更新需要绘制的对象（如移动对象的位置
        * 清除画布
        * 在画布上重新绘制对象
    * 循环实现
        * 递归方法 + 定时器方法setTime()
    * 中断和重启动画的方法
        * 采用按钮事件 + 布尔锁
    * 随机形状
        * 形状以对象的形式定义，赋予相关属性
        * 在每次循环开始前更改形状的属性
    * 圆周运动
        * 坐标(x, y)、圆半径(r)和偏移角度(a)的关系式
            * $x = r × Math.cos(a)$
            * $y = r × Math.sin(a)$
        * 圆周运动的原点位于当前坐标点左侧r个像素处
    * 反弹运动
        * 判断形状的坐标是否到达边界坐标，若到达则反向运动即可

8、高级动画
* 总体来说是给对象增加各种各样的物理属性，然后每次循环中按照一定物理规则来更改对象的属性来修改运动轨迹