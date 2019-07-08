---
title: WES7 裁剪过程
tags:
  - WES7
categories:
  - 技术文章
toc: true
date: 2019-07-08 15:30:58
---

## 软件安装

- Standard_7SP1_Toolkit [下载](https://www.microsoft.com/en-us/download/details.aspx?id=11887)
- Standard 7 SP1 64bit IBW  [下载](https://www.microsoft.com/en-us/download/details.aspx?id=11887)

## 裁剪步骤

1. 加载 Standard 7 SP1 64bit IBW.iso 并复制DS到某个目录下;
2. 打开ICE，然后【File】-【Select Distribution Share】选择DS所在路径;
3. 【File】-【New Answer File】，然后点击左侧Templates里面的Thin Client.xml和Windows_Touch_Support_for_N-Trig_Displays.xml 添加最小系统和触摸相关的组件;
4. 使用 Standard 7 SP1 64bit IBW.iso 中source路径下的tap.exe在目标计算机上执行 tap.exe /o d:\device.pmq生成硬件相关的配置;
5. 在ICE中点击【File】-【Import】-【Import PMQ】导入在目标机上生成的硬件信息，此时会根据硬件信息集成相关驱动到镜像中;
6. 官网下载中文语言包，lp.cab ，在ICE中点击【File】-【Import Packages】导入中文语言包;
7. 添加自定义驱动，将驱动放到Out-of-Box Driver中并右键【Insert Driver Path to Pass 1 windowPE】;
8. 然后在Answer File中对FeaturePack进行裁剪;
9. 裁剪完成以后，点击ICE下的【Tools】-【Create Media】-【Create IBW Image From Answer File…】, 将镜像生成到某个路径下；

## 生成镜像文件

打开ICE带的命令行，输入以下命令生成镜像文件
```SHELL
set wes7Path=E:\work\WES7\output\wes7_sna_x64
cd ../amd64
oscdimg.exe -m -o -n -lWES7_SNA_x64 -b%wes7Path%\BOOT\ETFSBOOT.COM %wes7Path% %wes7Path%.iso
```

也可以创建批处理命令来进行操作

```SHELL
::设置镜像路径
set wes7Path=E:\work\WES7\output\wes7_sna_x64

::配置ICE环境
call "C:\Program Files (x86)\Windows Embedded Standard 7\Tools\PETools\pesetenv.cmd"
cd ../amd64

::生成镜像
oscdimg.exe -m -o -n -lWES7_SNA_x64 -b%wes7Path%\BOOT\ETFSBOOT.COM %wes7Path% %wes7Path%.iso

pause
```

## 打包成 *.wim
暂无