---
title: Three.js学习笔记
tags:
  - threejs
categories:
  - Javascript
photos:
  - 
mathjax: false
description: Three.js学习摘要
date: 2016-04-27 21:55:33
---

* Three.js —— 一个Javascript的3D库
* 学习Three.js的笔记
* 参考教材《Three.js入门指南》
* demo地址：[https://github.com/chenjiefly/my-three.js](https://github.com/chenjiefly/my-three.js)

{% asset_img logo.jpg 图片来源：bing %}

<!--more-->

## Three.js学习笔记
### 一、学习教材：Three.js入门指南
### 二、工程内容：书中的例子
### 三、文件组织：按照章节顺序
### 四、API参考：[http://threejs.org/docs/](http://threejs.org/docs/)
### 五、重要内容摘录
#### 1、three.js程序基本组成
* 一个典型的 Three.js 程序至少要包括渲染器(Renderer)、场景(Scene)、照相机 (Camera),以及在场景中创建的物体。
* 渲染器
    * 渲染器将和 Canvas 元素进行绑定
    * 绑定已存在的Canvas元素：
    
    ```
    var renderer = new THREE.WebGLRenderer({
        canvas: document.getElementById('mainCanvas')
    });
    ```
    * 渲染器生成Canvas元素：
    
    ```
    var renderer = new THREE.WebGLRenderer();
    renderer.setSize(400, 300); 
    document.getElementsByTagName('body')[0].appendChild(renderer.domElement);
    ```
    * 设置画面背景色：
    
    ```
    renderer.setClearColor(0x000000)
    ```
* 场景
    * 在Three.js中添加的**物体都是添加到场景中的**,因此它相当于一个大容器。一般说,场景 来没有很复杂的操作,在程序最开始的时候进行实例化,然后将物体添加到场景中即可。
    * 生成场景：
    
    ```
    var scene = new THREE.Scene();
    ```
* 照相机
    * three.js坐标系：右手坐标系
        * x（正向水平向右），y（正向垂直向上），z（垂直于屏幕向电脑屏幕内侧）
    * 透视投影照相机：
    
    ``` 
    var camera = new THREE.PerspectiveCamera(45, 4 / 3, 1, 1000); 
    camera.position.set(0, 0, 5);
    scene.add(camera);
    ```
* 几何学物体
    * 单位长度1
    
      这里的长度是在物体坐标系中的,其单位与屏幕分辨 率等无关,简单地说,它就是一个虚拟空间的坐标系, 代表多少并没有实际的意义,而 重要的是相对长度。

    * 长方体

    ```
    // 创建一个 x、y、z 方向长度分别为1、2、3的长方体,并将其设置为红色
    var cube = new THREE.Mesh(new THREE.CubeGeometry(1, 2, 3), 
        new THREE.MeshBasicMaterial({
            color: 0xff0000 
        })
    );
    scene.add(cube);  // 添加长方体到场景中
    ```
* 渲染
    * 在定义了场景中的物体,设置好的照相机之后,渲染器就知道如何渲染出二维的结果

    ```
    renderer.render(scene, camera);  // 调用渲染器的渲染函数,就能使其渲染一次
    ```

#### 2、照相机
* 照相机就是这样一个抽象,它定义了三维空间到二维屏幕的投影方式，用“照相机”这样一个类比,可以使我们直观地理解这一投影方式。
* 正交投影照相机
    * 正交影照相机获得的结果就像我们在数学几何学课上老师教我们画的效果,对于在三维空间内 平行的线,投影到二维空间中也一定是平行的
    
    {% asset_img 5-2-1.png 正交投影照相机 %}

    * 构造函数

    ```
    THREE.OrthographicCamera(left, right, top, bottom, near, far)
    ```
    这六个参数分别代表正交投影照相机拍摄到的空间的六个面的位置，这两个面围成一个长 方体,我们称其为视景体(Frustum)。只有在视景体内部(下图中的灰色部分)的物体才可能显示在屏幕上，而视景体外的物体会在显示之前被裁减掉。

    {% asset_img 5-2-2.png 正交投影照相机模型 %}

    * 注意事项

    **为了保持照相机的横竖比例,需要保证 (right - left) 与 (top - bottom) 的比例与 Canvas 宽度与高度的比例一致。**

* 透视投影照相机
    * 透视投影照相机获得的结果是类似人眼在真实世界中看到的有“近大远小”的效果
    
    {% asset_img 5-2-3.png 透视投影照相机模型 %}

    * 构造函数

    ```
    THREE.PerspectiveCamera(fov, aspect, near, far)
    ```
    灰色部分为视景体，是可能被渲染的物体的所在区域。
    fov是视景体垂直方向张角（角度制而非弧度制），aspect等于width/height，是照相机水平方向和竖直方向长度的比值,通常设为 Canvas 的横纵比例。
    near和far分别是照相机到视景体最近、最远的距离,均为正值,且far应大于near。

    {% asset_img 5-2-4.png 透视投影照相机模型 %}

* 照相机视角
    * lookAt()方法**只接受THREE.Vector3的实例**

    ```
    camera.lookAt(new THREE.Vector3(0, 0, 0));
    ```

#### 3、基本几何形状
* 长方体 cube
    * 默认位置
        * 物体的默认位置是原点，对于立方体而言，其几何中心在原点的位置
    * 构造函数

    ```
    THREE.CubeGeometry(width, height, depth, widthSegments, heightSegments, depthSegments)
    ```

    前三个分别是x、y和z轴方向上的长度，后三个参数分别表示x、y和z轴方向上的分段数（缺省为1）

* 平面 plane
    * 构造函数

    ```
    THREE.PlaneGeometry(width, height, widthSegments, heightSegments)
    ```

    width是x方向上的长度，height是y方向上的长度，后两个参数是分段数。
    如果需要创建的平面在x轴和z轴所在的平面内，可以通过物体的旋转来实现。

* 球体 sphere
    * 构造函数

    ```
    THREE.SphereGeometry(radius, segmentsWidth, segmentsHeight, phiStart, phiLength, thetaStart, thetaLength)
    ```

    radius是半径，segmentWidth表示经度切片数，segmentHeight表示纬度切片数，phiStart表示经度开始弧度，phiLength表示经度跨过的弧度，thetaStart表示纬度开始的弧度，thetaLength表示纬度跨过的弧度

* 圆形 circle
    * 构造函数

    ```
    THREE.CircleGeometry(radius, segments, thetaStart, thetaLength)
    ```

    radius是半径，segments是切片数，thetaStart表示纬度开始的弧度，thetaLength表示纬度跨过的弧度

* 圆柱体 cylinder
    * 构造函数

    ```
    THREE.CylinderGeometry(radiusTop, radiusBottom, height, radiusSegments, heightSegments, openEnded)
    ```

    radiusTop是上底面半径，radiusBottom是下地面半径，height是柱体高度，radiusSegments和heightSegments都是切片数，openEnded表示是否没有上下底面，缺省值为false，表示有上下底面

* 正四面体
    * 构造函数

    ```
    THREE.TetrahedronGeometry(radius, detail)
    ```
    radius为半径，detail是细节层次(Level of Detail)的层数,对于大面片数模型,可以控制在视角靠近物体时,显示面片数多的精细模型,而在离物体较远时,显示面片数较少的粗略模型。

* 正八面体
    * 构造函数

    ```
    THREE.OctahedronGeometry(radius, detail)
    ```
* 正十二面体
    * 构造函数

    ```
    THREE.IcosahedronGeometry(radius, detail)
    ```

* 圆环面
    * 构造函数

    ```
    THREE.TorusGeometry(radius, tube, radialSegments, tubularSegments, arc)
    ```

    radius是圆环半径，tube是管道半径，radialSegments和tubularSegments是分段数，arc是圆环面的弧度，缺省值为Math.PI * 2

* 圆环节
    * 构造函数

    ```
    THREE.TorusKnotGeometry(radius, tube, radialSegments, tubularSegments, p, q, heightScale)
    ```

    其他参数同圆环面，p和q是控制样式的参数，一般可以缺省，heightScale是在z轴上的缩放

* 文字形状
    * 构造函数

    ```
    THREE.TextGeometry(text, parameters)
    ```

    parameters是以下参数组成的对象：
    * size：字号大小，一般为答谢字母的高度
    * height：文字的厚度
    * curveSegments：弧线分段数，使得文字的曲线更加光滑
    * font：字体，默认是“helvetiker”，需对应引用的字体文件
    * weight：值为“normal”或“bold”，表示是否加粗
    * style：值为“normal”或“italics”，表示是否斜体
    * bevelEnabled：布尔值，是否使用倒角，意为在边缘处斜切
    * bevelThickness：倒角厚度
    * bevelSize：倒角宽度

* 自定义形状
    * 基本方法：手动指定每个顶点位置,以及顶点连接情况
    * 建议方法：在 3ds Max 之类的建模软件中创建模 型,然后使用 Three.js 导入到场景中
    * 类：Geometry，该类是其他基本几何形状的父类
    * 构造函数

    ```
    var geometry = THREE.Geometry();    // 构造函数
    geometry.vertices.push(new THREE.Vector3(x, y, z));     // 设置形状顶点位置方法
    geometry.faces.push(new THREE.Face3(dot1, dot2, dot3)); // 创建三个顶点组成的面片，三个参数分别是三个顶点在geometry.vertices中的序号
    geometry.faces.push(new THREE.Face4(dot1, dot2, dot3, dot4));  // face4在新版中已经被移除，使用两个face3替代，如下的face4()所示
    
    /**
     * [face4 创建四个顶点的平面]
     * @param  {[Object]} geometry [自定义形状对象]
     * @param  {[Number]} dot1     [平面端点1]
     * @param  {[Number]} dot2     [平面端点2]
     * @param  {[Number]} dot3     [平面端点3]
     * @param  {[Number]} dot4     [平面端点4]
     */
    function face4(geometry, dot1, dot2, dot3, dot4) {
        geometry.faces.push(new THREE.Face3(dot1, dot2, dot3));
        geometry.faces.push(new THREE.Face3(dot2, dot3, dot4));
    }
    ```

* 注意
    * 对于wireframe，R59版本使用四边形来渲染形状，R60版本开始使用三角形面来渲染形状
    * 对于R60后的版本，如果只是需要不带对角线的长方体，可使用BoxHelper
    * 关于该问题可参考[https://github.com/mrdoob/three.js/issues/3788](https://github.com/mrdoob/three.js/issues/3788)

#### 4、材质
* 材质

    独立于物体顶点信息之外的与渲染效果相关的属性。通过设置材质可以改变物体的颜色、纹理贴图、光照模式等。

* 基本材质 BasicMaterial
    * 渲染后物体的颜色始终为该材质的颜色,而不会 由于光照产生明暗、阴影效果。如果没有指定材质的颜色,则颜色是随机的
    * 构造函数

    ```
    THREE.MeshBasicMaterial(opt)
    ```

    opt可缺省，是包含属性的对象：
        * color：十六进制RGB颜色，如0x0000ff
        * wireframe：是否渲染线而非面，默认为false
        * visible：是否可见，默认为true
        * side：渲染面片正面或反面，默认为正面THREE.FrontSide，反面为THREE.BackSide，或双面THREE.DoubleSide
        * map：使用纹理贴图

* Lambert材质 MeshLambertMaterial
    * Lambert光照模型的主要特点是只考虑漫反射而不考虑镜面反射的效果,因而对于金属、镜子等需要镜面反射效果的物体就不适应,对于其他大部分物体的漫反射效果都是适用的。
    * 构造函数

    ```
    new THREE.MeshLambertMaterial(opt)
    ```
    
    opt可缺省，是包含属性的对象：
        * color：十六进制RGB颜色，如0x0000ff
        * emissive：自发光颜色，可用来表现光源，如打开可表现太阳自发光的红色，关闭可表现地球半阴影效果
        * wireframe：渲染线而非面，默认为true

* Phong 材质
    * Phong光照模型考虑了镜面反射的效果,因此对于金属、镜面的表现尤为适合。
    * 构造函数

    ```
    new THREE.MeshPhongMaterial(opt)
    ```
    
    opt可缺省，是包含属性的对象：
        * color：十六进制RGB颜色，如0x0000ff
        * emissive：自发光颜色，可用来表现光源，如打开可表现太阳自发光的红色，关闭可表现地球半阴影效果
        * wireframe：渲染线而非面，默认为true

* 法向材质
    * 将材质的颜色设置为其法向量的方向,有时候对于调试很有帮助。
    * 构造函数

    ```
    new THREE.MeshNormalMaterial()
    ```

* 纹理贴图
    * 方法 THREE.ImageUtils.loadTexture(url, mapping, onLoad, onError)

    其中onLoad是一个方法，功能是在导入纹理图片成功后执行回调，未使用动画时可以执行一次渲染重绘，保证纹理导入成功后可见

    ```
    /**************** 单纹理图片贴满正方体 ****************/

    // 导入纹理图像
    var texture = THREE.ImageUtils.loadTexture('../img/0.png', {}, function() {
        renderer.render(scene, camera);
    });

    // 使用纹理图
    var material = new THREE.MeshLambertMaterial({
        map: texture 
    });

    /**************** 6张纹理图片贴在正方体6个面商 ****************/

    // 导入纹理图像
    var materials = [];
    for (var i = 0; i < 6; i++) {
        materials.push(new THREE.MeshBasicMaterial({
            map: THREE.ImageUtils.loadTexture('../../img/' + i + '.png', {}, function() {
                renderer.render(scene, camera);
            }),
            overdraw: true
        }));
    }
    
    // 创建正方体
    var cube = new THREE.Mesh(new THREE.CubeGeometry(3, 3, 3), 
        new THREE.MeshFaceMaterial(materials)  // 注意此处的用法
    );
    
    scene.add(cube);
    ```

    * 纹理复制

    ```
    var texture = THREE.ImageUtils.loadTexture('../img/chess.png', {}, function() {
        renderer.render(scene, camera);
    });
    texture.wrapS = texture.wrapT = THREE.RepeatWrapping;
    texture.repeat.set(4, 4);
    var material = new THREE.MeshLambertMaterial({
        map: texture
    });
    
    var plane = new THREE.Mesh(new THREE.PlaneGeometry(12, 12), material);
    ```

#### 5、网格
* 网格(Mesh)是由顶点、边、面等组成的一种物体，其他物体还包括线段(Line)、骨骼 (Bone)、粒子系统(ParticleSystem)等
* 更改网格属性
    * 即使已经创建了网格物体，也可以更改网格的属性，如材质

    ```
    // 材质
    var material = new THREE.MeshLambertMaterial({
        color: 0xffff00
        // wireframe: true
    });

    // 创建长方体
    var geometry = new THREE.CubeGeometry(1, 2, 3, 2, 2, 3);
    var cube = new THREE.Mesh(geometry, material);
    scene.add(cube);

    // 更改网格属性
    cube.material = new THREE.MeshLambertMaterial({
        color: 0xffff00,
        wireframe: true
    });
    ```

* 位置、缩放和旋转
    * 位置 position

    ```
    mesh.position.x = 1;  // 设置单个属性
    mesh.position.set(x, y, z);  // 设置多个属性
    ```

    * 缩放 scale
    
    ```
    mesh.scale.x = 2;  // 设置单个属性
    mesh.scale.set(x, y, z);  // 设置多个属性
    ```

    * 旋转 rotation

    ```
    mesh.rotation.x = 90;  // 设置单个属性
    mesh.rotation.set(x, y, z);  // 设置多个属性
    ```

#### 6、动画
* 每秒帧数 FPS(Frames Per Second)
    * 一般取30~60之间

* setInterval 方法
    * id = setInterval(func, msec)
    * func是回调函数，msec是毫秒数，返回id值
    * 适合保证程序的运算不至于导致延迟的情况下提供更加简洁的逻辑（无需自行处理时间），但是不一定保证时间间隔每次都相同
* clearInterval 方法
    * clearInterval(id)
    * 参数id为setInterval方法返回的值
* requestAnimationFrame 方法
    * id = requestAnimationFrame(func)
    * func是回调函数，不需要时间间隔，间隔时间会由浏览器自行决定
    * 适合对于时间较为敏感的环境，但是动画逻辑更加复杂
    * **注意：各浏览器中该方法名称可能不一致，需要使用如下代码检测**

    ```
    var requestAnimationFrame = window.requestAnimationFrame ||   // 检查浏览器支持的方法名称
                                window.mozRequestAnimationFrame || 
                                window.webkitRequestAnimationFrame || 
                                window.msRequestAnimationFrame;
    window.requestAnimationFrame = requestAnimationFrame;
    ```

* cancelAniamtionFrame 方法
    * cancelAnimationFrame(id)
    * 参数id为requestAnimationFrame方法返回的值
    * **注意：各浏览器中该方法名称可能不一致，需要使用如下代码检测**

    ```
    var cancelAnimationFrame = window.cancelAnimationFrame ||   // 检查浏览器支持的方法名称
                                window.mozCancelAnimationFrame || 
                                window.webkitCancelAnimationFrame || 
                                window.msCancelAnimationFrame;
    window.cancelAnimationFrame = cancelAnimationFrame;
    ```

#### 7、外部模型
* three.js支持3dMax导出的模型文件，如*.obj，*.mtl等格式
* 有材质的模型
    * 可导入无材质模型后再设置材质
    * 也可在建模软件中导出材质信息

#### 8、光与影
* 常用光源模型

环境光、平行光、点光源和聚光灯

* 环境光
    * 定义

    环境光是指场景整体的光照效果，是由于场景内若干光源的多次反射形成的亮度一致的效果,通常用来为整个场景指定一个基础亮度。因此，环境光没有明确的光源位置，在各处形成的亮度也是一致的。

    * 构造函数

    ```
    THREE.AmbientLight(hex)
    ```

    参数hex为环境光指定的颜色，是十六进制的RGB值。

    * 环境光强度

    0xffffff表示最强的白色，0xcccccc表示灰色（实际就是减弱后的白光）

    * 注意

    MeshLambertMaterial方法的配置对象属性中的ambient似乎在新版中被删除了，因此与教程中略有出入。
    就测试而言，材质的color属性中对应颜色通道如果开启，就可以反射环境光中对应的颜色通道。

* 点光源
    * 定义

    点光源是不计光源大小，可以看作一个点发出的光源。点光源照到不同物体表面的亮度是线性递减的，因此，离点光源距离越远的物体会显得越暗。
    
    * 构造函数

    ```
    THREE.PointLight(hex, intensity, distance)
    ```

    hex是光源的十六进制的颜色值；intensity是亮度，缺省值为1，表示100%的亮度；distance是光源最远照射到的距离，缺省值为0.


* 平行光
    * 定义

    对于任意平行的平面，平行光照射的亮度都是相同的，而与平面所在位置无关，只与平面法向量相关。
    对于平行光而言，**设置光源位置尤为重要**。

    * 构造函数

    ```
    THREE.DirectionalLight(hex, intensity)
    ```

    hex是光源的十六进制颜色值；intensity是亮度，缺省值为1，表示100%的亮度。

    * 光源位置

    使用`light.position.set(x, y, z)`设置光源位置后，平ing光将以(-x, -y, -z)的矢量方向照射到所有平面。

* 聚光灯
    * 定义

    一种特殊的点光源，能够朝着一个方向投射光线，投射出类似圆锥形的光线

    * 构造函数

    ```
    THREE.SpotLight(hex, intensity, distance, angle, exponent)
    ```

    前三个参数同点光源，angle为聚光灯的张角，缺省值是Math.PI/3，最大值是Math.PI/2；
    exponent是光强在target的衰减指数（target需要在之后定义，缺省值为(0, 0, 0)），缺省值是10 

    * 物体跟随

    让聚光灯跟随物体移动可使用如下代码

    ```
    light.target = mesh;  // mesh是物体形状对象
    ```

    * 聚光灯照射到某一点

    ```
    light.position.set(x1, y1, z1);
    light.target.position.set(x2, y2, z2);
    ```

    光线将从(x1, y1, z1)照射到(x2, y2, z2)方向

* 阴影
    * 能形成阴影的光源只有THREE.DirectionalLight与THREE.SpotLight
    * 能表现阴影效果的材质只有THREE.LambertMaterial与THREE.PhongMaterial
    * 步骤一：通知渲染器渲染阴影

    ```
    renderer.shadowMap.enabled = true;  // 与教程中不同，新版库做修改了
    ```

    * 步骤二：设置光源和所有要产生阴影的物体

    ```
    light.castShadow = true;
    mesh.castShadow = true;
    ```

    * 步骤三：设置接受阴影的物体

    ```
    mesh.receiveShadow = true;
    ```

    * 步骤四：设置光源阴影的相关属性，类似照相机的视景区

    ```
    // 对于聚光灯
    light.shadowCameraNear = 2;  // 只有介于shadowCameraNear和shadowCameraFar之间的物体将产生阴影
    light.shadowCameraFar = 10;
    light.shadowCameraFov = 30;  // 阴影张角

    // 对于平行光
    light.shadowCameraNear = 2;  // 只有在下面六个参数围成的长方体内的物体才会产生阴影效果
    light.shadowCameraFar = 10;
    light.shadowCameraLeft = 2;
    light.shadowCameraRight = 10;
    light.shadowCameraTop = 2;
    light.shadowCameraBottom = 10;
    ```

    * 步骤五：开启阴影照相机位置可视开关（用于观察阴影照相机位置）

    ```
    light.shadowCameraVisible = true;
    ```

    * 步骤六：调整阴影深浅

    ```
    light.shadowDarkness = 0.3;  // 范围是0~1，越小越浅
    ```
