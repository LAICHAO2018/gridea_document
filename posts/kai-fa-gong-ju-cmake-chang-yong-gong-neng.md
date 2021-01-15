---
title: '[开发工具] CMake常用功能'
date: 2020-11-07 15:37:06
tags: [技术,C++]
published: true
hideInList: false
feature: /post-images/kai-fa-gong-ju-cmake-chang-yong-gong-neng.jpg
isTop: false
---
## 1. CMake介绍
+ CMake是一个<font color=red>跨平台的构建工具</font>，可以用简单的语句来描述所有平台的安装(编译过程)。能够输出各种各样的makefile或者project文件。CMake 并不直接建构出最终的软件，而是产生其他工具的脚本（如Makefile ），然后再依这个工具的构建方式使用。
+ CMake是一个比make更高级的编译配置工具，它可以根据不同平台、不同的编译器，生成相应的Makefile或者vcproj项目。从而达到跨平台的目的。Android Studio利用CMake生成的是ninja，ninja是一个小型的关注速度的构建系统。我们不需要关心ninja的脚本，知道怎么配置cmake就可以了。从而可以看出cmake其实是一个跨平台的支持产出各种不同的构建脚本的一个工具。

## 2. CMake基本使用
#### 1. 添加头文件目录INCLUDE_DIRECTORIES
语法：
```
include_directories([AFTER|BEFORE] [SYSTEM] dir1 [dir2 ...])
```
它相当于g++选项中的-I参数的作用，也相当于环境变量中增加路径到CPLUS_INCLUDE_PATH变量的作用。
```
include_directories(../../../thirdparty/comm/include)
```
#### 2. 添加需要链接的库文件目录LINK_DIRECTORIES
语法：
```
link_directories(directory1 directory2 ...)
```
它相当于g++命令的-L选项的作用，也相当于环境变量中增加LD_LIBRARY_PATH的路径的作用。
```
link_directories("/home/server/third/lib")
```
#### 3. 查找库所在目录FIND_LIBRARY
语法：
```
A short-hand signature is:

find_library (<VAR> name1 [path1 path2 ...])
The general signature is:

find_library (
          <VAR>
          name | NAMES name1 [name2 ...] [NAMES_PER_DIR]
          [HINTS path1 [path2 ... ENV var]]
          [PATHS path1 [path2 ... ENV var]]
          [PATH_SUFFIXES suffix1 [suffix2 ...]]
          [DOC "cache documentation string"]
          [NO_DEFAULT_PATH]
          [NO_CMAKE_ENVIRONMENT_PATH]
          [NO_CMAKE_PATH]
          [NO_SYSTEM_ENVIRONMENT_PATH]
          [NO_CMAKE_SYSTEM_PATH]
          [CMAKE_FIND_ROOT_PATH_BOTH |
           ONLY_CMAKE_FIND_ROOT_PATH |
           NO_CMAKE_FIND_ROOT_PATH]
)
```
cmake会在目录中查找，如果所有目录中都没有，值RUNTIME_LIB就会被赋为NO_DEFAULT_PATH
```
FIND_LIBRARY(RUNTIME_LIB rt /usr/lib  /usr/local/lib NO_DEFAULT_PATH)
```
#### 4. 添加需要链接的库文件路径LINK_LIBRARIES
语法：
```
link_libraries(library1 <debug | optimized> library2 ...)
```
可以链接一个，也可以多个，中间使用空格分隔.
```
# 直接是全路径link_libraries(“/home/server/third/lib/libcommon.a”)
# 下面的例子，只有库名，cmake会自动去所包含的目录搜索link_libraries(iconv)# 传入变量link_libraries(${RUNTIME_LIB})
# 也可以链接多个link_libraries("/opt/MATLAB/R2012a/bin/glnxa64/libeng.so"　"/opt/MATLAB/R2012a/bin/glnxa64/libmx.so")
```
#### 5. 设置要链接的库文件的名称TARGET_LINK_LIBRARIES 
语法：
```
target_link_libraries(<target> [item1 [item2 [...]]]
                      [[debug|optimized|general] <item>] ...)
```
例如：
```
# 以下写法都可以： 
target_link_libraries(myProject comm)       # 连接libhello.so库，默认优先链接动态库
target_link_libraries(myProject libcomm.a)  # 显示指定链接静态库
target_link_libraries(myProject libcomm.so) # 显示指定链接动态库

# 再如：
target_link_libraries(myProject libcomm.so)　　#这些库名写法都可以。
target_link_libraries(myProject comm)
target_link_libraries(myProject -lcomm)
```
#### 6. 为工程生成目标文件
语法：
```
add_executable(<name> [WIN32] [MACOSX_BUNDLE]
               [EXCLUDE_FROM_ALL]
               source1 [source2 ...])
```
简单的例子如下：
```
add_executable(demo
        main.cpp
)
```
## 3. CMake案例
贴一个完整的例子:
(另外，使用cmake生成makefile之后，make edit_cache可以编辑编译选项。)
```
cmake_minimum_required (VERSION 2.6)

INCLUDE_DIRECTORIES(../../thirdparty/comm)

FIND_LIBRARY(COMM_LIB comm ../../thirdparty/comm/lib NO_DEFAULT_PATH)
FIND_LIBRARY(RUNTIME_LIB rt /usr/lib  /usr/local/lib NO_DEFAULT_PATH)

link_libraries(${COMM_LIB} ${RUNTIME_LIB})

ADD_DEFINITIONS(
-O3 -g -W -Wall
 -Wunused-variable -Wunused-parameter -Wunused-function -Wunused
 -Wno-deprecated -Woverloaded-virtual -Wwrite-strings
 -D__WUR= -D_REENTRANT -D_FILE_OFFSET_BITS=64 -DTIXML_USE_STL
)

add_library(lib_demo
        cmd.cpp
        global.cpp
        md5.cpp
)

link_libraries(lib_demo)
add_executable(demo
        main.cpp
)

# link library in static mode
target_link_libraries(demo libuuid.a)
```
## 4. 结束语
CSDN上的[这篇博客](https://blog.csdn.net/zdaiot/article/details/83066168)也讲的十分详细可以去看看
## 5. 参考博客
+ [https://www.cnblogs.com/binbinjx/p/5626916.html](https://www.cnblogs.com/binbinjx/p/5626916.html)

