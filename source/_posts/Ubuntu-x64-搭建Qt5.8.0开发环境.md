---
title: Ubuntu x64 搭建Qt5.8.0开发环境
tags:
  - 嵌入式
categories: []
toc: false
urlname: ubuntu-x64-Qt580
translate_title: ubuntu-x64-build-qt580-development-environment
date: 2018-06-14 00:00:00
---

> 在64bit系统下搭建qt5.80 32bit的交叉编译环境

1. 参照说明文档，复制交叉编译器 **arm-gcc** 和 **qt5.8.0** 到ubuntu 虚拟机中

2. 解压文件到指定路径 arm-gcc (`/usr/local/arm/arm-2014.05/`)  和 Qt5.8.0（`/opt/Qt5.8.0/`）

3. 将编译器路径 `/usr/local/arm/arm-2014.05/bin` 添加到系统环境变量。 `vim ~/.bashrc`

   ```bash
   export PATH=/usr/local/arm/arm-2014.05/bin:$PATH
   export GCC_COLORS=auto
   ```

4. 执行该文件`source ~./bashrc` 使环境变量生效

5. 安装64bit下对应的32bit库，参考 [Ubuntu16.04编译Qt5.8（x86&arm）](https://blog.csdn.net/u010875635/article/details/72793957)

   ```bash
   apt-get install libc:i386 lib32ncurses5 lib32z1
   apt-get install libxcb-xinerama0-dev:i386
   sudo apt-get install libqt4-dev 或者 sudo apt-get install libgl1-mesa-dev 或者 libgl1-mesa-dev 或者 libglu1-mesa-dev
   sudo apt-get install '^libxcb.*-dev' libx11-xcb-dev libglu1-mesa-dev libxrender-dev libxi-dev
   sudo apt-get install build-essential perl python git
   sudo apt-get install gcc-multilib g++-multilib  libc6:i386 lib32z1
   sudo dpkg --add-architecture i386
   sudo apt-get dist-upgrade
   ```

6. 配置Qt 编译器和 Qt 版本
  【工具】-【选项】-【构建和运行】，分别设置 **编译器**、**Qt Version** 和 **构建套件**。