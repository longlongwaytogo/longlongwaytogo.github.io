---
layout: post
title: Deferred Technique
categories: [图形渲染]
description: deferred shading，deferred lighting 
keywords: deferred shading，deferred lighting 
---
# 1. Deferred Shading:
deferred shading 是一种屏幕空间着色技术。它被称为Deferred，是因为实际上
在第一次pass中的顶点和像素着色器中没有执行着色：相反，着色是“deferred（延迟）”,
到第二个pass中执行.

在deferred着色器的第一次pass中，只收集着色计算所需的数据。每个表面的位置、法线和材质，
然后使用“render to texture”技术渲染到几何缓冲（G-buffer）中。在这之后，像素着色器使用
屏幕空间中纹理缓冲区的信息计算每个像素的直接和间接光照。

# 2. Deferred lighting
Deferred Lighting（也被称为Light Pre-Pass）是对Deferred Shading的一种修改。
在Deferred shading中这个技术使用了三个Pass，而不是两个Pass。在第一遍pass场景几何图形时，
只将计算每像素光照（辐射度）所需的属性写入G-buffer中。屏幕空间，“Deferred”Pass只输出
漫反射和高光光照数据，所以必须在场景上进行第二遍Pass，以读取照明数据并输出最终的像素着色。
Deferred lighting的明显优势是大大减少了G-buffer的大小。明显的代价是需要渲染场景几何两次
而不是一次。另一个额外的成本是在Deferred lighting中的Deferred pass必须分别输出漫反射和镜面辐射，
而Deferred shading中的Deferred pass只需要输出单个的组合辐射值。由于减小了G-buffer的大小,
这种技术可以克服Deferred shading的一个严重缺点——多材质。另一个可以解决的问题是MSAA。
Deferred lighting可以在DirectX9硬件上与MSAA一起使用。
---------------------------------------------------------------------------------------
#reference
[跨平台渲染引擎之路--Rendering Path](https://zhuanlan.zhihu.com/p/58817407)
[Deferred Shading 延迟着色（翻译）](https://www.cnblogs.com/gozili/p/9558580.html)
[Deferred Shading VS Deferred Lighting](https://blog.csdn.net/bugrunner/article/details/7436600)
[osg deferred rendering](https://pdfs.semanticscholar.org/c9df/de95975ab241c42d96450dba2902029c3a64.pdf)
[](http://publications.lib.chalmers.se/records/fulltext/219034/219034.pdf)
---------------------------------------------------------------------------------------
