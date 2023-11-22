---
title: Qt配置VTK
date: 2023-11-02 11:27:37.0
updated: 2023-11-02 20:29:50.037
url: /archives/qtwithvtk
categories: 
- 编译与配置
tags: 
- VTK
---

# 前言

假设电脑环境已经有qt，cmake了，只差VTK

# 下载VTK

点击去下载[VTK source](https://vtk.org/download/)

# 编译VTK

## 源码

解压下载好的VTK源码，并且我把源码的文件改名为`VTK_src`

![image-20231102162957881](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202311021630983.png)

## CMake-gui路径选择

打开CMake-gui，选择一个源码路径与输出路径output_dir

![image-20231102163135013](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202311021631056.png)

## CMake生成文件选择

点击`Configure`  选择`MinGW Makefiles`与`Specify native compilers`

![image-20231102163258864](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202311021632905.png)

## 编译器选择

点击`Next`  选择`~/Tools/mingw1120_64/bin/gcc.exe`和`~/Tools/mingw1120_64/bin/g++.exe`

![image-20231102163644018](https://imagere.oss-cn-beijing.aliyuncs.com/img20230227/202311021636051.png)

点击`Finish`之后，等等进度条跑完

## CMake编译配置

- 注意配置选项
    - CMAKE_BUILD_TYPE：如果需要Release的话就选择Release，否则Debug即可。作者选的Release
    - CMAKE_INSTALL_PREFIX：安装路径，自行选择一个文件夹即可。记住这个install文件夹
    - VTK_BUILD_DOCUMENTATION：不勾选，因为需要doxygen，如果报这个错误，可以取消勾选，反正documentation可以去网上看
    - VTK_BUILD_EXAMPLES：可以选择勾选
    - VTK_BUILD_TESTING：选择OFF
    - VTK_GROUP_ENABLE_Qt：选择yes

再点击`Configure`，应该有报错，需要选择Qt路径

- 继续配置，选择自己的Qt版本，我这里是Qt6
    - Qt6_DIR：选择自己的Qt安装路径，`~/6.5.0/mingw_64/lib/cmake/Qt6`
    - VTK_QT_VERSION：选择自己的版本

继续点击`Configure`，然后还有错误，再次点击`Configure`，直到没有红色即可。

然后点击`Generate`，没有错误

## make编译

切换到输出路径output_dir，我的是`~\VTK\VTKoutput`，请看[前面](CMake-gui路径选择)输出路径是什么，然后在终端打开

还记得[前面](#编译器选择)有个路径是：`~/Tools/mingw1120_64/bin/gcc.exe`的吗？然后这里需要用到的可执行文件是`~\Tools\mingw1120_64\bin\mingw32-make.exe`

Linux是make，Windows是mingw32-make

输入以下命令进行编译

```shell
~/Tools/mingw1120_64/bin/mingw32-make.exe -j8 # 请选择自己的路径，并且这个 8 表示编译的核心数
```

等待编译完成

## make安装

```shell
~/Tools/mingw1120_64/bin/mingw32-make.exe install
```







