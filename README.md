# Cesium
Study for 3D-GIS
## 2021.9.24 
- 今日目标：
1. 学习Celery（×）
2. 联系与完善Celery与Redis后端（×）
3. 学习Cesium 3D开发（×）
## 2021.10.12
- 学习WebGL（Three.js）
###学习笔记（2021.10.12）
- 三要素：场景、相机、渲染
- `requestAnimationFrame`方法实现渲染循环（游戏循环）
- Stats.js 性能监视器  Tween.js 动画引擎
~~~
function initTween()
{
    new TWEEN.Tween( mesh.position)
            .to( { x: -400 }, 3000 ).repeat( Infinity ).start();
}
~~~
- TWEEN.Tween的构造函数接受的是要改变属性的对象，这里传入的是mesh的位置。Tween的任何一个函数返回的都是自身，所以可以用串联的方式直接调用各个
函数。to函数，接受两个参数，第一个参数是一个集合，里面存放的键值对，键x表示mesh.position的x属性，值-400表示，动画结束的时候需要移动到的位置。
第二个参数，是完成动画需要的时间，这里是3000ms。repeat( Infinity )表示重复无穷次，也可以接受一个整形数值，例如5次。Start表示开始动画，默认
情况下是匀速的将mesh.position.x移动到-400的位置。
~~~
function animation()
{
    renderer.render(scene, camera);
    requestAnimationFrame(animation);
    stats.update();
    TWEEN.update();
}
~~~
- 其中的TWEEN.update()完成了让动画动起来到目标。如果不调用这个函数场景就不能动起来了。如下为样例代码（需要本地引入三个js文件）：
~~~
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>Three框架</title>
		<script src="js/Three.js"></script>
        <script src="js/Stats.js"></script>
		<script src="js/tween.min.js"></script>

		<style type="text/css">
			div#canvas-frame {
				border: none;
				cursor: pointer;
				width: 100%;
				height: 600px;
				background-color: #EEEEEE;
			}

		</style>
		<script>
            var renderer;
            var stats;

            function initThree() {
                width = document.getElementById('canvas-frame').clientWidth;
                height = document.getElementById('canvas-frame').clientHeight;
                renderer = new THREE.WebGLRenderer({
                    antialias : true
                });
                renderer.setSize(width, height);
                document.getElementById('canvas-frame').appendChild(renderer.domElement);
                renderer.setClearColor(0xFFFFFF, 1.0);

                stats = new Stats();
                stats.domElement.style.position = 'absolute';
                stats.domElement.style.left = '0px';
                stats.domElement.style.top = '0px';
                document.getElementById('canvas-frame').appendChild(stats.domElement);
            }

            var camera;
            function initCamera() {
                camera = new THREE.PerspectiveCamera(45, width / height, 1, 10000);
                camera.position.x = 0;
                camera.position.y = 0;
                camera.position.z = 600;
                camera.up.x = 0;
                camera.up.y = 1;
                camera.up.z = 0;
                camera.lookAt({
                    x : 0,
                    y : 0,
                    z : 0
                });
            }

            var scene;
            function initScene() {
                scene = new THREE.Scene();
            }

            var light;
            function initLight() {
                light = new THREE.AmbientLight(0xFF0000);
                light.position.set(100, 100, 200);
                scene.add(light);
				
                light = new THREE.PointLight(0x00FF00);
                light.position.set(0, 0,300);
                scene.add(light);
            }

            var cube;
			var mesh;
            function initObject() {
                var geometry = new THREE.CylinderGeometry( 100,150,400);
                var material = new THREE.MeshLambertMaterial( { color:0xFFFFFF} );
                mesh = new THREE.Mesh( geometry,material);
                mesh.position = new THREE.Vector3(0,0,0);
                scene.add(mesh);
            }

            function threeStart() {
                initThree();
                initCamera();
                initScene();
                initLight();
                initObject();
				initTween();
                animation();

            }
			function initTween()
			{
				new TWEEN.Tween( mesh.position)
						.to( { x: -400 }, 3000 ).repeat( Infinity ).start();
			}
            function animation()
            {
                renderer.render(scene, camera);
                requestAnimationFrame(animation);
				
                stats.update();
				TWEEN.update();
            }

		</script>
	</head>

	<body onload="threeStart();">
		<div></div>
		<div id="canvas-frame"></div>
	</body>
</html>
~~~
- 你好，我使用了TWEEN.js,代码跟例子一样，为什么显示 repeat()这个方法未定义呢，改成easing就可以 ！！！
- 在Threejs中相机的表示是THREE.Camera，它是相机的抽象基类，其子类有两种相机，分别是正投影相机（正交投影相机）THREE.OrthographicCamera和
透视投影相机THREE.PerspectiveCamera。
- 正投影和透视投影的区别是：透视投影有一个基本点，就是远处的物体比近处的物体小。在工程建筑领域，正投影的例子很多，其特点是，远近高低比例都相同。
- OrthographicCamera( left, right, top, bottom, near, far )，PerspectiveCamera( fov, aspect, near, far )
~~~
1、 left参数

left：左平面距离相机中心点的垂直距离。从图中可以看出，左平面是屏幕里面的那个平面。

2、 right参数

right：右平面距离相机中心点的垂直距离。从图中可以看出，右平面是屏幕稍微外面一点的那个平面。

3、 top参数

top：顶平面距离相机中心点的垂直距离。上图中的顶平面，是长方体头朝天的平面。

4、 bottom参数

bottom：底平面距离相机中心点的垂直距离。底平面是头朝地的平面。

5、near参数

near：近平面距离相机中心点的垂直距离。近平面是左边竖着的那个平面。

6、far参数

far：远平面距离相机中心点的垂直距离。远平面是右边竖着的那个平面。

1、视角fov：这个最难理解,我的理解是,眼睛睁开的角度,即,视角的大小,如果设置为0,相当你闭上眼睛了,所以什么也看不到,如果为180,那么可以认为你的视界很
广阔,但是在180度的时候，往往物体很小，因为他在你的整个可视区域中的比例变小了。

2、近平面near：这个呢，表示你近处的裁面的距离。补充一下，也可以认为是眼睛距离近处的距离，假设为10米远，请不要设置为负值，Three.js就傻了,不知道
怎么算了。

3、远平面far：这个呢，表示你远处的裁面。

4、纵横比aspect：实际窗口的横纵比，即宽度除以高度。这个值越大，说明你宽度越大，那么你可能看的是宽银幕电影了，如果这个值小于1，那么可能你看到的是
如下的图中的LED屏幕了。

简单的透视相机例子：

var camera = new THREE.PerspectiveCamera( 45, width / height, 1, 1000 );
scene.add( camera );
~~~
- 最近问微软的Babylon.js的同学比较多，Babylon.js的相机相比Three.js更灵活。
- 光源基类：在Threejs中，光源用Light表示，它是所有光源的基类。它的构造函数是：THREE.Light ( hex )，它有一个参数hex，接受一个16进制的颜色值。
例如要定义一种红色的光源，我们可以这样来定义：Var redLight = new THREE.Light(0xFF0000);
- THREE.Light只是其他所有光源的基类，要让光源除了具有颜色的特性之外，我们需要其他光源。看看，下面的类图，是目前光源的继承结构。可以看出，所有的
具体光源都继承与THREE.Light类。下面我们来具体看一下，其他光源。
- 当没有任何光源的时候，最终的颜色将是黑色，无论材质是什么颜色。
- 环境光就是在场景中无处不在的光，它对物体的影响是均匀的，也就是无论你从物体的那个角度观察，物体的颜色都是一样的，这就是伟大的环境光。你可以把环境
光放在任何一个位置，它的光线是不会衰减的，是永恒的某个强度的一种光源。
- 环境光（THREE.AmbientLight）、点光源（THREE.PointLight）、聚光灯（THREE.SpootLight）、区域光（THREE.AreaLight）、方向光（THREE.DirectionalLight）
###学习笔记（2021.10.13）
- 方向光的构造函数如下：THREE.DirectionalLight = function ( hex, intensity )，其参数如下：Hex：关系的颜色，用16进制表示。Intensity：
光线的强度，默认为1。因为RGB的三个值均在0~255之间，不能反映出光照的强度变化，光照越强，物体表面就更明亮。它的取值范围是0到1。如果为0，表示光线基
本没什么作用，那么物体就会显示为黑色。方向由位置和原点（0,0,0）来决定，方向光只与方向有关，与离物体的远近无关。
- 通过调整整个程序中的camera.up参数，发现相机默认up轴与y轴同方向（0，1，0），up轴方向与向量（camera.up.x,camera.up.y,camera.up.z）同方向。
即（1，1，0）表示相机右转45度。（-1，1，0）左转45度。
- 什么是纹理，是图片，或者贴图。
- 在threejs中，纹理类由THREE.Texture表示，其构造函数如下所示：THREE.Texture( image, mapping, wrapS, wrapT, magFilter, minFilter, 
format, type, anisotropy )
- ①通过loadtexture函数在服务器上加载一张图片作为纹理 ②通过html中的canvas来作为纹理
###学习笔记（2021.10.14）
- 模型是由面组成，面分为三角形和四边形面。三角形和四边形面组成了网格模型。在Three.js中用THREE.Mesh来表示网格模型。THREE.Mesh可以和THREE.Line
相提并论，区别是THREE.Line表示的是线条。THREE.Mesh表示面的集合。THREE.Mesh，它的构造函数是：THREE.Mesh = function ( geometry, material )
其中第一个参数geometry：是一个THREE.Geometry类型的对象,他是一个包含顶点和顶点之间连接关系的对象。第二个参数Material：就是定义的材质。
有了材质就能够让模型更好看，材质会影响光照、纹理对Mesh的作用效果。
- Three.js支持很多种3D模型格式，例如ply，stl，obj，vtk等等。随着three.js的升级，会支持越来越多的文件格式，到目前为止，three.js已经能够支持市面上大多数3D模型格式了。
同时需要重点说明的是，如果认真理解完three.js对模型的加载、解析方法，那么写一种自己的3D文件解析器是非常便利的。
- Vtk模型是一种以文本方式表示的3D模型文件，其能够表示点面信息，而且能够以人类易读易懂的方式以文本的形式存储下来。在科学研究中，这种文件格式使用得非常多


