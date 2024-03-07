---
title: Jetson nano编译ethminer
date: 2022-11-16 08:08:51.228
updated: 2023-01-02 07:37:04.613
url: /archives/jetson编译ethminermd
categories: 
- 编译与配置
tags: 
---

## 前言

&emsp;装这个ethminer纯属只是为了玩，千万不要问我这个是干啥的，写下这篇博客只是为了记录一下当时遇到的一些问题。毕竟谁也不会真的用 `Jetson nano` 运行 `ethminer`，我用的还是 `Jetson nano 4GB` 版本的

## 步骤

[`github`仓库](https://github.com/ethereum-mining/ethminer)

[参考链接](http://www.singleye.net/2018/03/%E4%BD%BF%E7%94%A8nvidia-jetson-tx2%E6%8C%96%E4%BB%A5%E5%A4%AA%E5%B8%81/)

```shell
git clone https://github.com/ethereum-mining/ethminer
git submodule update --init --recursive
mkdir build
cd build
cmake .. -DETHASHCUDA=ON -DETHASHCL=OFF    # 配置CUDA，默认使用OpenCL
sudo make install


# 查看GPU信息
ethminer -U --list-devices
```

## 问题

1、Boost问题

`~/.hunter/_Base/Download/Boost/1.66.0/075d0b4/`

这个目录下面有个SHA1值，去网上下载同样SHA1值的boost文件，拷贝到这

2、OpenSSL问题

下载1.1.1和1.1.1a，也有SHA1值检查

3、不要用proxychains4代理

4、ethminer.info错误

``` Shell
Found a solution.
Need to add Boost::filesystem to target_link_libraries() to line 15 of ethminer/libhwmon/CMakeLists.txt. Looks like this for me:

target_link_libraries(hwmon ${HWMON_LINK_LIBRARIES} Boost::filesystem)
 
And I need remove all build files, then cmake again
```