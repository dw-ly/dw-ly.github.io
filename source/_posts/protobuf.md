---
title: protobuf
category:
 - 学习
tag:
 - linux
 - thirdparty
description: protobuf

---
# 安装指定版本的protobuf

## 一.直接使用apt安装

### 1.查询apt可以安装的版本

```bash
#apt-cache madison <<package name>>查询
apt-cache madison protobuf
```

如果是想要安装的版本，直接`apt-get install`,如果不是的话，可以通过一下方法安装。

参考：[【apt】查询和安装指定版本软件](http://www.manongjc.com/detail/28-wvncyqwzccgilxq.html)

## 二.通过git安装

### 1.确保依赖库已安装

```bash
sudo apt-get install autoconf automake libtool curl make g++ unzip
```

### 2.`git clone`源码

```bash
git clone -b v3.6.0 https://github.com/protocolbuffers/protobuf.git
#v后接想要安装的版本
cd protobuf
git submodule update --init --recursive
#安装protobuf的子模块， 主要是gtest
```

### 3.编译安装

```bash
cd protobuf
./autogen.sh
./configure
make
make check
sudo make install
sudo ldconfig
protoc --version
```

如果没有configure文件和makefile文件等。如安装3.0版本

```bash
cd protobuf
aclocal
autoconf
autoheader
automake --add-missing
./configure
make
sudo make install
```

### 4.报错及解决

**若有报错出现configure.ac:\*: error: required file ‘autoconf/ltmain.sh’ not found安装libtools**

```bash
sudo apt install libtool
libtoolize --automake --copy --debug --force
#未验证
```

**若有报错出现config.status: error: cannot find input file: `Makefile.in’**

```bash
aclocal
libtoolize -–automake #原文这里少一个中横杠 
automake --add-missing
./configure
make
sudo make install
```

参考：[ubuntu系统Protobuf指定版本安装，以及报错解决方法](https://www.codenong.com/cs106207763/)

**若有报错出现‘protoc: error while loading shared libraries: libprotoc.so.10: cannot open shared object file: No such file or directory’**

参考[protobuf报错解决](https://blog.csdn.net/qq_27563511/article/details/80696403)使用`export LD_LIBRARY_PATH=/usr/local/lib`临时解决

完整解决参考：[protobuf报错解决](https://blog.csdn.net/cc1949/article/details/105655377)

确认是否有ld指向文件 `/etc/ld.so.conf.d/libprotobuf.conf`,没有则创建，内容如下：

```bash
/usr/local/lib
```

然后回到`protobuf`目录(保险起见)执行`sudo ldconfig`

```bash
protoc --version
#libprotoc 3.0.0
```

