---
layout: post
title:osg场景状态和状态集第1部分
categories: [OSG]
description: 介绍OSG State、StateSet的使用
keywords: OSG,States,StateSets ，OpenGL
---


from:[http://www.bricoworks.com/articles/stateset/stateset.html]()

 ## **你一直想直到的关于StateSet的一切（但不敢来问**  
 作者： Tim Moore  
翻译：longlongwaytogo   
一些名词翻译： 
	    Open Scene Graph： OSG
    	scene graph ：场景树
		XXgraph state ：状态树
   
   对于OSG的优化策略，最好的办法就是降低OpenGL切换状态的次数，OpenGL被定义为一个复杂的状态机，其状态包含了能够影响多边形被绘制的所有内容。构成状态的值，从很容易使用on/off进行切换的状态值（如，启用深度测试（depth test)或者光照（lighting)),到涉及占用图形处理器数千或数百万字节存储单元且具有复杂结构类型的值（如，纹理和着色器程序).即使OpenGL驱动程序可能不需要将所有的的数据重新传输到显卡来改变状态，但它依然很昂贵。通常，当状态改变时，GPU上的所有渲染必须停止。更改着色器或者纹理的设置，可能需要更长的时间。  

   三维场景可以包含数千个不同的对象，但其中有很大一部分共享相同的图形状态。如果可以将相同状态的对象收集到一起渲染，则可避免多余的状态切换，并且状态更改的次数将被降低到最小。这些对象可以被程序进行有序的组织，但在类似OSG这样的图形系统中，对象所分布的逻辑和空间层次，不一定与对象使用的图形状态相关。

   在第一部分，我们将学习OSG如何体现OpenGL的图形状态。在第二部分，探讨OSG优化渲染的一些方法，以最大限度地减少状态变化的次数。  

## Drawable 和 StateSet
   在OSG中，几何图形被存储在场景图叶子节点的Drawable对象中。为了渲染，Drawable提供了一个简单的接口：draw()函数。当draw()函数被调用时，必须正确的设置OpenGL模型视图矩阵和所有其它状态。在每一帧绘制中，OSG都会遍历场景树。在一个被叫做"Culling"的处理过程中，确定了哪些Drawable对象是可见的，记录模型视图矩阵和图形状态，并将它们存储到叫做：RenderBins的列表中。有些RenderBins的内容将通过视点和模型的距离进行有序存储，但大多数的Drawbles会被分组到状态树中。当执行"draw"操作时，所有的Drawables将会按照顺序渲染出来。  

   OSG如何表示状态树并识别对象具有相同的状态呢？如果你已经编写过OSG程序，就会知道，你没有为场景树中的一个对象指整个图形状图；相反，你创建了一个名为StatSet的对象，它指定一小部分的OpenGL状态，并将其附加到场景树节点上。图1 表示了场景树的结构：

![](http://www.bricoworks.com/articles/stateset/graph.png)      
			           图1.简单的场景树


  StateSet可能是指定的纹理，灯光的材质信息，用于shander程序的Uniform参数，以及一些OpenGL模式（如：启用混合和alpha测试）。StateSet可以包含两种类型的值，模式类型和StateAtrribute对象的指针。模式类型是一个常整型的数值，在传递给glEnable()/glDisable()函数时控制OpenGL功能。StateAtrribute是OpenGL状态中的一个或一组值。属性直接对应OpenGL状态机中的参数，但某些OpenGL参数可能分组为单个StateAttribute属性，并且StateAttribute可能会有与任何实际OpenGL参数不相对的成员。这方面的例子是MaterialState属性，它将环境光（ambient），漫反射光(diffuse),镜面反射光(specular),以及自发光材质(emission material)属性组合到一个对象中，即使每一个参数在OpenGL中都可以独立修改。Material通常拥有setAphla()之类的函数来统一修改材质参数的alpha值；这在使用原始OpenGL时必须手工完成。StateSet内部表现是十分动态的，从StateSet的角度来看，它没有任何预定义的模式，只有系统已知的普通状态属性。StateSet根据纹理单元编号存储纹理模式和属性。  
![](http://www.bricoworks.com/articles/stateset/stateset.png)
   图 2 osg::stateSet  

  场景树中的所有节点，从场景树根节点，到叶子节点的Drawables,都可能关联了StateSet。当一个Drawable真正被绘制时，OpenGL的状态（它所具有的默认状态,以及它继承祖先的模式和属性）才会生效。树中较低的属性优先于树中StateSet较高对象设置的属性，尽管有一个系统可以使属性可以覆盖图中较低的属性的值，相反，属性可以保护自己免受被推翻。

## osg::State

 OSG通过State对象管理整个OpenGL状态。除非需要自定义Drawable类，否则通常不会遇到此对象的drawImplementation()函数。drawImplementation()函数，是OSG在渲染场景时广泛使用的。State使用与StateSet相同的模式和状态属性，在OpenGL中设图形状态。当前状态是通过StateSet对象的设计来最小化将要进行的低级别OpenGL调用的数量来改变的。需要注意的是，状态属性相等性仅基于StateAttribute 对象的标识或指针值。状态不会“查看”状态属性或调用它们的compare()方法。

## StateSets and the StateSet Stack 
  如前所述，一个StateSet，是一个小的模式和属性的集合，它将被应用到当前的图形状态中，State维护着一个Stateset对象的栈(Stack),它包含了修改图形状态的公有接口：pushStateSet(),popStateSet(),apply()方法。本质上讲，State维护着包含模式肯属性类型的Stacks。当调用pushStateSet()或popStateSet(),StateSet被遍历同，同时独立的模式和属性栈被push或popped。关于pushStateSet()和popStateSet(),重要的一点是，它们并没有真正修改OpenGL的状态。你必须调用apply()来强制OpenGL状态与所有模式和属性栈的当前顶一致。随着个人模式和属性的推出和弹出， State对象会做一些记录，以确定堆栈顶部的值是否真的发生了变化。apply()方法只更新实际更改的OpenGL模式和属性。这种惰性更新允许StateSet在实际改变OpenGL状态之前推动和弹出几个对象，这很可能与原始状态相似或相同。您可能会想象在StateSet遍历场景图时发生堆栈修改。OSG渲染遍历实际上并没有直接做到这一点，但它确实以类似堆栈的方式执行类似的一系列“移动”，推送和弹出StateSet对象。
![](http://www.bricoworks.com/articles/stateset/state.png)
 图 3 osg::State 

处理StateSet可能在OSG代码中看到的对象有一些便利方法。State::apply(const StateSet*)推送一个StateSet，调用apply()，然后StateSet一次性弹出全部。State::insertStateSet()弹出一些StateSets，插入一个新的StateSet，然后推回旧的。这个和其他类似的方法用于渲染遍历中的各种效果。也有applyMode()和applyAttribute() 该做出改变并标记参数的堆栈改变，即使没有被压入或弹出方法。这将强制该参数在下次呼叫时正确更新apply。  

## 在自定义Drawable中使用状态  

如前所述，通常您不会在OSG代码中使用State对象，但是如果您定义了自己的Drawable类，你需要重写drawImplementation()方法。有关使用State的示例，请查看OSG中osgText::Text的源代码，这会使许多OpenGL调用其绘制方法。除了管理保存在StateSet对象中的图形状态之外 ，State还控制较低级别的OpenGL状态，例如使用顶点属性数组。State也有用于调用可能作为扩展实现的OpenGL函数的成员函数。

 您应该在您自己的代码中使用状态管理功能，而不是相同功能的较低级别的OpenGL代码; 否则，OpenGL状态将与State对象模型不一致，导致视觉上错误甚至崩溃。另一种方法是使用glPushAttrib()和glPushClientAttrib() 函数将所有OpenGL状态保存在函数中，运行图形代码并使用glPopClientAttrib() 和恢复状态glPopAttrib()。这很慢，但如果包装遗留代码可能没有替代方案。

## 下一部分

第2部分描述了这个StateGraph类。该类被用于当OSG使用StateSet渲染场景并实际推送和弹出对象时。