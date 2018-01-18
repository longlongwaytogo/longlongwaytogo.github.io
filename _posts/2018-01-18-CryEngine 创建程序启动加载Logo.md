---
layout: post
title: CryEngine 创建程序启动加载Logo
categories: [CryEngineV]
description: 程序启动logo显示
keywords: CryEngineV UI logo
---


## 1. 背景

程序在启动过程中，有一段加载时间，此时可以加载一张背景图，以免看到加载过程中的日志输出。 对于CryEngine,它使用的是ScaleForm的flash作为UI进行显示。所以以下也采用flash来完成。

## 2. 具体步骤
####2.1 创建flash  
- 打开flash cs5,创建基于ActionScript2.0的flash文件（CryEngine不支持3.0）：
![](https://github.com/longlongwaytogo/CryEngineV_Proj/blob/master/Learn/5.3.x/ThirdPerson/docs/res/startMenuLogo_1.png?raw=true)
- 找一张背景图导入到舞台中：   
![](https://github.com/longlongwaytogo/CryEngineV_Proj/blob/master/Learn/5.3.x/ThirdPerson/docs/res/startMenuLogo_2.png?raw=true)
- 保存flash为：startMenuLogo.fla,并发布startMenuLogo.swf文件  
![](https://github.com/longlongwaytogo/CryEngineV_Proj/blob/master/Learn/5.3.x/ThirdPerson/docs/res/startMenuLogo_3.png?raw=true)
####2.2 创建菜单
  创建如下StartMenuLogo.xml文件：

		<UIElements name="StartMenuLogo">
		<UIElement name="StartMenuLogo" render_lockless="1" mouseevents="1" cursor="1" >
		 <GFX file="StartMenuLogo.swf" layer= "1" >
		   <Constraints>
				<Align mode = "fullscreen"   />
		   </Constraints>
		 </GFX>
		</UIElement>
		</UIElements>    


####2.3 创建UI的flowgraph 
- 打开或者创建一个CryEngine的模版程序
- 在libs/目录下创建ui目录，再在ui目录下创建UIElements目录，将StartMenuLogo.xml保存在此处，重启编辑器
- 打开场景，找到flowgraph视图，创建基于UI的flowgraph,命名为logo.xml:  
![](https://github.com/longlongwaytogo/CryEngineV_Proj/blob/master/Learn/5.3.x/ThirdPerson/docs/res/startMenuLogo_4.png?raw=true)  
- 在logo文件中，创建如下节点：  
![](https://github.com/longlongwaytogo/CryEngineV_Proj/blob/master/Learn/5.3.x/ThirdPerson/docs/res/startMenuLogo_5.png?raw=true)  
- 双击UI:Display:Dispaly的Element节点，弹出对话框，选择StartMenuLogo:   
![](https://github.com/longlongwaytogo/CryEngineV_Proj/blob/master/Learn/5.3.x/ThirdPerson/docs/res/startMenuLogo_6.png?raw=true) 
- 保存并关闭编辑器，使用lauch Game模式启动场景。 
![](https://github.com/longlongwaytogo/CryEngineV_Proj/blob/master/Learn/5.3.x/ThirdPerson/docs/res/startMenuLogo_7.png?raw=true)
![](https://github.com/longlongwaytogo/CryEngineV_Proj/blob/master/Learn/5.3.x/ThirdPerson/docs/res/startMenuLogo_8.png?raw=true)

## 3. 总结
以上就是参考官方的视频制作的启动UI，其中，有些地方还可以改进：

- 将swf文件通过gfxexpoter.exe工具转换为gfx格式
- 隐藏屏幕显示的输出文字
 可。