---
layout: post
title: Ubuntu编译gdb-ARM调试环境
urlname: ubuntu-arm-gdb
tags:
  - 嵌入式
  - Linux
  - Qt
translate_title: ubuntu-compiles-gdbarm-debugging-environment
permalink: 1531051707
date: 2018-07-08 20:08:27
---



参考[Qt可用的gdb编译，以及交叉编译gdbserver，以及配置QtCreator远程调试](https://blog.csdn.net/jiluoxingren/article/details/80844616)

<!--more-->

**编译脚本 **如下:

```bash
#!/bin/bash
echo -e "\033[32m 正在执行步骤一：检查python-dev包 \033[0m"
chkpydev=$(dpkg -l | grep python-dev)
if [ "$chkpydev" == "" ]; then
	sudo apt-get install python-dev --force-yes
fi
 
echo "#include <stdio.h>" > check.c
echo "#include <dlfcn.h>" >> check.c
echo "int main() { void *handle; void *pPyUnicodeUCS4_Decode = NULL; handle = dlopen(\"libpython2.7.so\", RTLD_LAZY); if (!handle)return 1; dlerror(); pPyUnicodeUCS4_Decode = dlsym(handle, \"PyUnicodeUCS4_Decode\"); if (dlerror() != NULL)return 2; dlclose(handle); return 3; }" >> check.c
gcc -rdynamic -o check check.c -ldl && ./check
checkpy=$?
rm check
rm check.c
 
echo -e "\033[32m 正在执行步骤二：gdb的configure \033[0m"
case $checkpy in
    1)
	echo "没有找到libpython2.7.so"
	exit
    ;;
    2)
	./configure CFLAGS="-O3" CXXFLAGS="-O3" --target=arm-none-linux-gnueabi --prefix=/opt/arm-none-linux-gnueabi-gdb/ --with-python
    ;;
    3)
	./configure CFLAGS="-DPy_UNICODE_WIDE -O3" CXXFLAGS="-DPy_UNICODE_WIDE -O3" --target=arm-none-linux-gnueabi --prefix=/opt/arm-none-linux-gnueabi-gdb/ --with-python
    ;;
esac
 
echo -e "\033[32m 正在执行步骤三：编译和安装gdb \033[0m"
make -j8
if [ $? -eq 0 ]; then
    make install
    echo -e "\033[32m gdb已安装到/opt/arm-none-linux-gnueabi-gdb/目录下 \033[0m"
else
    echo -e "\033[31m gdb编译失败 \033[0m"
    exit
fi
 
echo -e "\033[32m 正在执行步骤四：gdbserver的confiure \033[0m"
cd gdb/gdbserver
./configure CFLAGS="-O3" CXXFLAGS="-O3" --target=arm-none-linux-gnueabi --prefix=/opt/arm-none-linux-gnueabi-gdb/ --host=arm-none-linux-gnueabi
 
echo -e "\033[32m 正在执行步骤五：编译和安装gdbserver \033[0m"
make -j8
if [ $? -eq 0 ]; then
    make install
    echo -e "\033[32m gdbserver已安装到/opt/arm-none-linux-gnueabi-gdb/目录下 \033[0m"
else
    echo -e "\033[31m gdbserver编译失败 \033[0m"
    exit
fi
 
echo -e "\033[32m 正在执行步骤六：压缩可执行文件，将gdb放入交叉编译工具链 \033[0m"
cd /opt/arm-none-linux-gnueabi-gdb/bin/
cp arm-none-linux-gnueabi-gdbserver gdbserver
arm-none-linux-gnueabi-strip gdbserver
echo "arm-none-linux-gnueabi-strip gdbserver"
strip arm-none-linux-gnueabi-gdb arm-none-linux-gnueabi-run
echo "strip arm-none-linux-gnueabi-gdb arm-none-linux-gnueabi-run"
gdb_path=$(which arm-none-linux-gnueabi-gdb)
cross_path=${gdb_path%/*}/..
mv $(which arm-none-linux-gnueabi-gdb) $cross_path/bin/arm-none-linux-gnueabi-gdb.bk
echo "mv $cross_path/bin/arm-none-linux-gnueabi-gdb $cross_path/bin/arm-none-linux-gnueabi-gdb.bk"
cp arm-none-linux-gnueabi-gdb $cross_path/bin
echo "cp arm-none-linux-gnueabi-gdb $cross_path/bin"
mv $(which arm-none-linux-gnueabi-run) $cross_path/bin/arm-none-linux-gnueabi-run.bk
echo "mv $cross_path/bin/arm-none-linux-gnueabi-run $cross_path/bin/arm-none-linux-gnueabi-run.bk"
cp arm-none-linux-gnueabi-run $cross_path/bin
echo "cp arm-none-linux-gnueabi-run $cross_path/bin"
cd ..
cp -rf share $cross_path
echo "cp -rf share $cross_path"
echo -e "\033[32m 已经将新的arm-none-linux-gnueabi-gdb放入交叉编译工具链目录$cross_path/bin \033[0m"
echo -e "\033[32m 完成 \033[0m"
```

