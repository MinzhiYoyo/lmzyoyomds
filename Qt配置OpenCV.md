---
title: Qt配置OpenCV
id: 53
date: 2024-12-03 17:09:51
auther: yutanbird
cover: 
excerpt: 在Qt下配置好OpenCV并且运行
permalink: /archives/qt%E9%85%8D%E7%BD%AEopencv
categories:
 - comb
tags: 
 - opencv
---



# 下载

## 下载Qt

[在线下载](https://d13lb3tujbc8s0.cloudfront.net/onlineinstallers/qt-unified-windows-x64-4.5.1-online.exe)

[安装包下载](https://download.qt.io/official_releases/qt/6.4/6.4.1/single/)

## 下载`CMake`

也可以用`Qt`的`cmake`

[点击下载](https://github.com/Kitware/CMake/releases/download/v3.25.1/cmake-3.25.1-windows-x86_64.msi)

## 下载Git

[点击下载](https://github.com/git-for-windows/git/releases/download/v2.39.0.windows.2/Git-2.39.0.2-64-bit.exe)

## 下载OpenCV

&emsp;建立一个空白文件夹`~/`，在里面创建两个文件夹`src`和`output`，`src`用来放`opencv`源码，`output`用来放编译输出，并且`output`也是以后`lib`的路径。

&emsp;在`~/`用`git bash`打开

&emsp;可能需要科学上网下载，需要配置`git`代理

```shell
git clone https://github.com/opencv/opencv.git  # 下载opencv
git clone https://github.com/opencv/opencv_contrib.git # 下载opencv_contrib扩展包，根据需要来

# 网络问题配置git代理，注意这里配置的是全局Git代理
git config --global http.proxy socks://127.0.0.1:7890
git config --global https.proxy socks://127.0.0.1:7890
```

# 环境变量

&emsp;值得注意的是，我们`make`，`gcc`和`g++`都可以用Qt的，前面`cmake`也可以用`Qt`的。下面这些路径需要添加到环境变量

***make其实在Windows下全称是mingw32-make***

注意下面路径，后面需要用到的

- `cmake`：`~\Qt\Tools\CMake_64\bin`
- `make,g++,gcc`： `~\Qt\Tools\mingw1120_64\bin`

# 编译

## 设置源目录以及输出目录

![image-20230101224801424](https://imagere.oss-cn-beijing.aliyuncs.com/img20221228/image-20230101224801424.png)

## 设置C++和C

![image-20230101224834650](https://imagere.oss-cn-beijing.aliyuncs.com/img20221228/image-20230101224834650.png)

- `g++`: `~\Qt\Tools\mingw1120_64\bin\g++.exe`
- `gcc`: `~\Qt\Tools\mingw1120_64\bin\gcc.exe`

![image-20230101224855766](https://imagere.oss-cn-beijing.aliyuncs.com/img20221228/image-20230101224855766.png)

![image-20230101225023712](https://imagere.oss-cn-beijing.aliyuncs.com/img20221228/image-20230101225023712.png)

## 修改编译选项

&emsp;需要多次`configure`，直到所有红色项消失，不能一次性配置好所有红色项

***注意我这里是相对路径，需要填写绝对路径***

- `CMAKE_MAKE_PROGRAM`: `~/Qt/Tools/mingw1120_64/bin/mingw32-make.exe`(前面没有勾选Advanced才会出现)
- `WITH_OPENGL`: √
- `WITH_QT`: √
- `WITH_CUDA`: √（可选，**我选了出错还没解决**）
- `OPENCV_EXTRA_MODULES_PATH`: `~/Src/opencv_contrib` （可选）

点击`Configure`，然后还需要进行配置

- `Qt5_DIR或者Qt6_DIR`: `~/Qt/6.4.1/mingw_64`（配置一个就行）
- `QT_QMAKE_EXECUTABLE`: `~/Qt/Tools/CMake_64/bin/cmake.exe`（qt6使用`cmake`编译）

点击`Configure`

没有错误后，点击`Generate`

## 正式编译

前面`CMAKE`生成的`Makefile`在之前设置的`~/Output`下面

打开`cmd`，`cd`到`~/output`

```shell
mingw32-make -j 8 # 数字表示核心数，多线程编译，根据自己电脑的核心数来

# 没有错误执行下面
mingw32-make install
```

都没错误表示ok了

# 配置

## 路径信息

- `install路径`：`~/Output/install`（后面需要的`lib`头文件什么的都在这）
- 头文件：`~\Output\install\include`
- `LIB`：`~\Output\install\x64\mingw`

## Qt配置

新建一个`Qt`工程，注意选择***MinGW64***而不是用的`MSVC`

在Qt配置文件中加入如下两行，加在配置文件位置任意，就加到`FORMS`下面就行

***一定要用绝对路径***

```cmake
INCLUDEPATH += ~\Output\install\include
LIBS += ~\Output\install\x64\mingw\lib\libopencv_*.a
LIBS += -L$$PWD/OpenCV -llibopencv_calib3d470 -llibopencv_core470 -llibopencv_dnn470 -llibopencv_features2d470 -llibopencv_flann470 -llibopencv_gapi470 -llibopencv_highgui470 -llibopencv_imgcodecs470 -llibopencv_imgproc470 -llibopencv_ml470 -llibopencv_objdetect470 -llibopencv_photo470 -llibopencv_stitching470 -llibopencv_video470 -llibopencv_videoio470
```

## 环境变量配置

运行时候，需要配置`dll`的，因此，我们把`~\Output\install\x64\mingw\bin`添加到环境变量，注意是绝对路径

配置完环境变量，需要重启Qt，不然Qt获得的环境变量没有更新的。

## 测试

```c++
#include <opencv2/opencv.hpp>

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MainWindow)
{
    ui->setupUi(this);
    cv::VideoCapture capture(0);
    cv::Mat src;
    capture.read(src);
    cv::imshow("测试", src);
}
```

测试如上代码，运行

![image-20230101235154682](https://imagere.oss-cn-beijing.aliyuncs.com/img20221228/image-20230101235154682.png)

## 打包程序

![image-20230101235412353](https://imagere.oss-cn-beijing.aliyuncs.com/img20221228/image-20230101235412353.png)

新建一个文件夹，名字我取`QtCV`，然后把`Release`版本的`exe`复制到该文件夹下

`Release`版本的`exe`路径是：`新建Qt工程的路径\QtCV\build-qtcv-Desktop_Qt_6_4_1_MinGW_64_bit-Release\release\qtcv.exe`

![image-20230101235910409](https://imagere.oss-cn-beijing.aliyuncs.com/img20221228/image-20230101235910409.png)

点击下面红框框的Qt控制台窗口

![image-20230101235531153](https://imagere.oss-cn-beijing.aliyuncs.com/img20221228/image-20230101235531153.png)

`cd`到上面新建的文件夹`QtCV`

```shell
cd ~/QtCV
windeployqt qtcv.exe
```

自动把需要的运行文件复制过来了，将整个文件夹打包发给一台没有配置的电脑，看是否能够运行，这样子是不能运行的，因为`windeployqt`并没有帮你把`opencv`的库复制过去。手动复制`dll`到该目录下

![image-20230102000129034](https://imagere.oss-cn-beijing.aliyuncs.com/img20221228/image-20230102000129034.png)

![image-20230102002921318](https://imagere.oss-cn-beijing.aliyuncs.com/img20221228/image-20230102002921318.png)

然后其他电脑才能运行