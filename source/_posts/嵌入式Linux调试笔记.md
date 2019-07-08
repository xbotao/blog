---
title: 嵌入式Linux调试笔记
tags:
  - 嵌入式
categories:
  - 随手小记
toc: false
urlname: emLinux-debug-note
translate_title: embedded-linux-debugging-notes
date: 2018-07-03 00:00:00
---

> 本文主要记录嵌入式Linux在开发调试过程中遇到的问题

### 1. JIT Issue

**错误描述**

   ```bash
   JIT is disabled for QML. Property bindings and animations will be very slow. Visit https://wiki.qt.io/V4 to learn about possible solutions for your platform.
   ```

**解决方案：**

1. 参考[Qt:QML 在嵌入式linux下环境变量设置](https://blog.csdn.net/x356982611/article/details/71036775)设置qml环境变量，修改 `/etc/profile` 。参照这种方式，最终并未得到解决

   ```bash
export QTDIR=/opt/Qt5.8.0
export QML_IMPORT_PATH=
export QML2_IMPORT_PATH=$QTDIR/qml
   ```

2. 参考 [(solved) JIT is disabled for QML](https://forums.gentoo.org/viewtopic-t-1077510-start-0-postdays-0-postorder-asc-highlight-.html?sid=458b9ef438aa1ebda6584fd2b2d15a6b) ， 该问题在Qt5.9.1以下版本中普遍存在，需要升级Qt版本到5.9.2以上或者 **KDE Frameworks** 版本 5.4.0以上