
## 概念

### HDR 文件 与 HDRI

* HDRI是具体的文件格式或一种图像内容，通常包含全景图像，能够表示环境光照信息和反射。在3D渲染中，HDRI常用作背景和全局光源，这样可以产生更加真实的阴影和反射效果。它通常是以特定格式存储的高动态范围图像（如.exr或.hdr）。[hdri-haven](https://hdri-haven.com/hdri/morning-racing-circuit#google_vignette)

* HDR指的是一种处理图像的方法或技术，旨在捕捉和显示更宽的亮度范围，使得图像中的细节在明亮和暗淡区域都能更好地体现。HDR技术可以在多种场合使用，比如在拍摄时合成多张不同曝光的照片（HDR合成），或在视频中使用动态范围扩展技术。

### 环境贴图
环境贴图（Environment Map）是一种在3D计算机图形中使用的技术，用于模拟和渲染物体的背景、反射和光照效果

包括以下类型：

* 立方体贴图：由六个正方形图像组成，分别表示六个方向（前、后、上、下、左、右）。这些图像拼合成一个立方体，使得场景中的对象能够基于视点动态地反射环境。

* 全景图：通常是一张360度的全景图，用于表示整个环境。
[相关提问](https://photo.stackexchange.com/questions/42941/how-to-make-spherical-360-panoramic-image-with-21-aspect-ratio)

### 360° Footage
什么是360° 镜头
https://msd.unimelb.edu.au/belt/home/belt-resources/virtual-site-visits/kaltura-360-video


### 全景与平面图的原理
* 等距柱面投影 和 立方体贴图

全景影像与全景球面的关系: https://blog.csdn.net/qq114480/article/details/105705668

全景图转局部平面视图原理详解: https://www.cnblogs.com/riddick/p/10258216.html

* VR 全景图手绘原理论文

使用直尺、指南针和量角器绘制等距柱状 VR 全景图: https://revistas.ucp.pt/index.php/jsta/article/view/7498

为沉浸式全景图绘制等矩形透视图: http://www.univ-ab.pt/~aaraujo/equirectangular.html

尺子、指南针和钉子的全球面透视构造: https://arxiv.org/abs/1511.02969

## 制作
1. 用一种360全景网格图辅助，绘制全景图。PS需要支持3d视图的版本
https://www.jb51.net/photoshop/573043.html

2. 6方格合成全景图，PS插件 Flexify2.0
https://www.jb51.net/photoshop/573043.html


3. 手绘球形全景图
http://paulheaston.blogspot.com/2020/11/blog-post_14.html


4. Hugin - 全景照片拼接器
https://hugin.sourceforge.io/

## Threejs 实现

1. 6面环境贴图 + 后处理颜色 + 后处理贴图
https://threejs.org/examples/webgl_postprocessing_backgrounds.html
```js
CubeTextureLoader
```


2. 6面环境贴图，360度环境贴图
https://threejs.org/examples/webgl_materials_envmaps.html
```js
const textureLoader = new THREE.TextureLoader();
textureEquirec = textureLoader.load( 'textures/2294472375_24a3b8ef46_o.jpg' );
textureEquirec.mapping = THREE.EquirectangularReflectionMapping;
textureEquirec.colorSpace = THREE.SRGBColorSpace;
scene.background = textureEquirec;
sphereMaterial.envMap = textureEquirec;
sphereMaterial.needsUpdate = true;


const loader = new THREE.CubeTextureLoader();
loader.setPath( 'textures/cube/Bridge2/' );
textureCube = loader.load( [ 'posx.jpg', 'negx.jpg', 'posy.jpg', 'negy.jpg', 'posz.jpg', 'negz.jpg' ] );
scene.background = textureCube;
sphereMaterial.envMap = textureCube;
sphereMaterial.needsUpdate = true;
```

3. 360度 HDR 环境贴图 + 导出EXR图
https://threejs.org/examples/misc_exporter_exr.html
```js
const rgbeloader = new RGBELoader();

//

const pmremGenerator = new THREE.PMREMGenerator( renderer );
pmremGenerator.compileEquirectangularShader();

rgbeloader.load( 'textures/equirectangular/san_giuseppe_bridge_2k.hdr', function ( texture ) {

    texture.mapping = THREE.EquirectangularReflectionMapping;

    renderTarget = pmremGenerator.fromEquirectangular( texture );
    scene.background = renderTarget.texture;

} );
```

4. 天空盒 + 海洋
https://threejs.org/examples/webgpu_ocean.html


5. 球形全景图，球内视角实现全景 + 添加标记
https://segmentfault.com/a/1190000019068732?utm_source=sf-related