---
layout: post
title: Ubuntu18.04 x64下配置Qt5.9.6交叉编译环境
urlname: ubuntu1804-Qt5.9.6-compline
key: 20180705
date: 2018-07-05
tags:
  - 嵌入式
  - Linux
  - Qt
---


# 开发包

- **arm-2014.05** : ARM交叉编译器
- **tslib1.4** ：通用触摸屏驱动
- **qt-everywhere-opensource-src-5.9.6.tar.xz** : Qt5.9.6源码


<!--more-->

# 开发机环境配置

## 1、配置交叉编译器环境

1. 解压`arm-2014.05.tar.gz` 到 `/user/local/arm/` 目录下

2. 添加路径到环境变量。修改（系统环境变量）`/etc/profile` 或者（用户环境变量）`~/.bashrc`，修改完成以后，执行`source /etc/profile`或`source ~/.bashrc`

   ```bash
   export PATH=$PATH:/usr/local/arm/arm-2014.05/bin
   ```

   ​

3. 添加到环境变量以后，在命令行窗口内输入`arm`并连续按2次 **Tab** 键，如果出现以下内容，则环境配置正确。

   ```bash
   xxxx@xxxx-pc:~$ arm
   arm2hpdl                           arm-none-linux-gnueabi-gcc-nm
   arm-none-linux-gnueabi-addr2line   arm-none-linux-gnueabi-gcc-ranlib
   arm-none-linux-gnueabi-ar          arm-none-linux-gnueabi-gcov
   arm-none-linux-gnueabi-as          arm-none-linux-gnueabi-gdb
   arm-none-linux-gnueabi-c++         arm-none-linux-gnueabi-gprof
   arm-none-linux-gnueabi-c++filt     arm-none-linux-gnueabi-ld
   arm-none-linux-gnueabi-cpp         arm-none-linux-gnueabi-nm
   arm-none-linux-gnueabi-cs          arm-none-linux-gnueabi-objcopy
   arm-none-linux-gnueabi-cs-daemon   arm-none-linux-gnueabi-objdump
   arm-none-linux-gnueabi-elfedit     arm-none-linux-gnueabi-ranlib
   arm-none-linux-gnueabi-g++         arm-none-linux-gnueabi-readelf
   arm-none-linux-gnueabi-gcc         arm-none-linux-gnueabi-size
   arm-none-linux-gnueabi-gcc-4.8.3   arm-none-linux-gnueabi-strings
   arm-none-linux-gnueabi-gcc-ar      arm-none-linux-gnueabi-strip
   ```

   ​


## 2、 编译tslib

参考 [QT5.7交叉编译安装到arm](https://blog.csdn.net/u012175418/article/details/52704734)



## 3、编译Qt源码

### 3.1 构建自动编译脚本

```bash
#!/bin/sh
./configure \
-release \
-opensource \
-confirm-license \
-prefix /opt/Qt5.9.6_arm \
-xplatform linux-arm-gnueabi-g++ \ #编译平台与下面的qmake所在文件夹应相同
-no-opengl \
-c++std c++11 \
-no-dbus \
-nomake examples \
-nomake tests \
-tslib \
-skip qtpurchasing \
-I/opt/tslib1.4/include -L/opt/tslib1.4/lib

```

### 3.2 修改qmake

```bash
gedit qtbase/mkspecs/linux-arm-gnueabi-g++/qmake.conf
```

修改文档内容如下:
```bash
#
# qmake configuration for building with arm-linux-gnueabi-g++
#

MAKEFILE_GENERATOR      = UNIX
CONFIG                 += incremental
QMAKE_INCREMENTAL_STYLE = sublib

QT_QPA_DEFAULT_PLATFORM = linux #eglfs
QMAKE_CFLAGS_RELEASE += -O2 -march=armv7-a
QMAKE_CXXFLAGS_RELEASE += -O2 -march=armv7-a

include(../common/linux.conf)
include(../common/gcc-base-unix.conf)
include(../common/g++-unix.conf)

# modifications to g++.conf
QMAKE_CC                = arm-none-linux-gnueabi-gcc
QMAKE_CXX               = arm-none-linux-gnueabi-g++
QMAKE_LINK              = arm-none-linux-gnueabi-g++
QMAKE_LINK_SHLIB        = arm-none-linux-gnueabi-g++

# modifications to linux.conf
QMAKE_AR                = arm-none-linux-gnueabi-ar cqs
QMAKE_OBJCOPY           = arm-none-linux-gnueabi-objcopy
QMAKE_NM                = arm-none-linux-gnueabi-nm -P
QMAKE_STRIP             = arm-none-linux-gnueabi-strip
load(qt_config)

```

## 4、编译帮助文档

```bash
make docs
nmake install_docs
```

## 5、配置Qt环境

参考 [QT5.7交叉编译安装到arm](https://blog.csdn.net/u012175418/article/details/52704734)


---
**Qt交叉编译参考文档:**

- [qt4.8.7交叉编译](https://blog.csdn.net/hjd03132301/article/details/74093777)
- [QT5.7交叉编译安装到arm](https://blog.csdn.net/u012175418/article/details/52704734)
- [【Qt】Ubuntu16.04编译Qt5.8（x86&arm）](https://blog.csdn.net/u010875635/article/details/72793957)
- [【Cubietruck】Qt5.8.0 x64、x86、armhf三个版本的编译安装](https://blog.csdn.net/u010875635/article/details/71055353)
- [嵌入式ARM+Linux 多个不同版本的交叉编译器的切换方法](https://blog.csdn.net/tcjy1000/article/details/50703960)