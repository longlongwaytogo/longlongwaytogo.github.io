---
layout: post
title: Create dlls on Windows without declspec
categories: [CMake]
description: CMake 自动导出符号生成dll库
keywords: CMake
---

-- 
author:longlongwaytogo  
link: [Learning.test/cmake/cmake_auto_export at master · longlongwaytogo/Learning.test · GitHub](https://github.com/longlongwaytogo/Learning.test/tree/master/cmake/cmake_auto_export)

---
对于Liunx和Unix平台，默认的c++模块都是以导出形式存在的，windows平台的导出需要声明__declspec(dlexport)进行导出，使用

__declspec(dllimport)进行导入，如果有一个静态库，现在想改成动态库，必须手动添加导出标志，除此，还有一个方法就是将导出信息，写入到*.def文件中，设置到编译器中。但这样都比较麻烦，正好CMake提供了自动生成def文件的功能。



## 1.  准备基础CMakeLists.txt和c++源文件：


## 1.1 c++源文件

### 1.1.1 DLL源码头文件

```cpp
#include <string>
class comm
{
public:
	static float sqrt(float v);
	float getValue();
	void setValue(float v);
private:
	float m_val;
};

std::string GetSystemName();

```

1.1.2 DLL源码cpp实现

```cpp
#include "comm.h"
float comm::s_val = 3.14f;
float comm::sqrt(float v)
{
	return v * v;
}

float comm::getValue()
{
	return m_val;
}

void comm::setValue(float v)
{
	m_val = v;
}

std::string GetSystemName()
{
	static std::string info = "windows 10";
	return info;
}
```

以上代码不手工添加导出，直接在CMake中设置全部导出便可导出

### 1.1.2 main.cpp

```cpp
#include <iostream>
#include "comm.h"

int main()
{
	std::cout <<" app start!"<< std::endl;
	std::cout <<"sqrt(100.0f):"<< comm::sqrt(100.0f)<<std::endl;
	std::cout << "current system info:" <<GetSystemName()<<std::endl;
	
	comm c;
	c.setValue(102.0f);
	std::cout << "c.getValue:" << c.getValue() << std::endl;

	return 0;
}

```



## 1.2 CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)
project(CMake_dyanmic_dll_test VERSION 1.0.0 LANGUAGES C CXX)

set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
set(BUILD_SHARED_LIBS ON)

add_library(comm comm.h comm.cpp)
add_executable(app main.cpp)
target_link_libraries(app comm)
```



 编译以上代码可以自动导出dll中的类，但对于全局的static函数是无法导出的，对于extern 或static变量，需要添加格外的导出，如下：

```cpp
class comm
{
public:
	static float sqrt(float v);
	float getValue();
	void setValue(float v);
private:
	
	float m_val;
public:
	COMM_API static float s_val;
};
COMM_API extern const float s_cval;
std::string GetSystemName();

// 静态函数不能被dll导出
// static int GetSystemNum();
```
# reference：
code：
[https://github.com/longlongwaytogo/Learning.test/tree/master/cmake/cmake_auto_export](https://github.com/longlongwaytogo/Learning.test/tree/master/cmake/cmake_auto_export)
[https://www.kitware.com//create-dlls-on-windows-without-declspec-using-new-cmake-export-all-feature/](https://www.kitware.com//create-dlls-on-windows-without-declspec-using-new-cmake-export-all-feature/)
