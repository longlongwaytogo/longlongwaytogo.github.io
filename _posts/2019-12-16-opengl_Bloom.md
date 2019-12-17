---
layout: post
title: OpenGL Bloom
categories: [图形渲染, OpenGL]
description: OpenGL Bloom 
keywords: Bloom, OpenGL
---
Bloom:全局泛光

Bloom，中文翻译为全局泛光，或者也成为了glow(辉光),他可以使图像的明亮部分会向较暗处稍微扩展。
泛光效果可以突出显示图像的主要成分，还可以用来模拟衍射(光从窗口穿过时发生的物理现象)效果。

**Bloom 效果实现的基本原理：(基于OpenGl)**
	1：渲染整个场景到FBO 先获取屏幕图像;
	2: 获取场景图像，对每个像素进行亮度检测，若大于某个阀值即保留原始颜色值，否则置为黑色；
	3：对上一步获取的图像，做一个模糊，通常使用高斯模糊。
	4: 将模糊后的图片和原图片做一个加权和。
 

#link
[盒式滤波器Box Filter](https://www.cnblogs.com/lwl2015/p/4460711.html)
[OpenGL实现的Bloom效果](https://github.com/candy9599/openGL-Bloom-effect)

[webgl 实现的Bloom效果预览](https://ykob.github.io/glsl-bloom/)
[webgl 实现的Bloom效果github](https://github.com/ykob/glsl-bloom)
[泛光效果的GLSL实现](https://zhuanlan.zhihu.com/p/60882950)
[nvdia bloom 例子](https://gameworksdocs.nvidia.com/GraphicsSamples/BloomSample.htm)
[unity屏幕泛光效果](https://www.jianshu.com/p/630fae9842af)
[Unity Shader-后处理：Bloom全屏泛光](https://www.cnblogs.com/lancidie/p/8665261.html)
[](http://www.devacg.com/?post=520)
[Unity3d HDR和Bloom效果（高动态范围图像和泛光）](https://www.cnblogs.com/zhanlang96/p/4312963.html)
感觉nv的例子效果一般，weblg和opengl实现的例子比较好
[gkENGINE HDR流程简析](https://www.cnblogs.com/gameknife/archive/2013/04/26/3046110.html)
---------------------------------------------------------------------------------------
# 其他(临时记在此处)
[体积光效果](https://blog.csdn.net/ZJU_fish1996/article/details/87533029)
[Bloom效果](https://zhuanlan.zhihu.com/p/50208005)
[跨平台渲染引擎之路--Rendering Path](https://zhuanlan.zhihu.com/p/58817407)

---------------------------------------------------------------------------------------
