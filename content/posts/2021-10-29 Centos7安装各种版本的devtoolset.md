---
title: CentOS 7安装各种版本的devtoolset-3
date: 2021-10-29 11:34:00
categories: ["cpp"]
---

### devtoolset介绍

devtoolset是为 Scientific Linux CERN 5 和 6 平台上的开发人员提供的产品。使用 Software Collections 框架，根据 UNIX 文件系统层次结构标准的建议，将一组额外的工具安装到 /opt 目录中。这些工具由用户使用提供的 scl 实用程序按需启用。

### devtoolset与gcc的版本对应关系

devtoolset-3对应gcc4.9.x版本
devtoolset-4对应gcc5.x.x版本
devtoolset-6对应gcc6.x.x版本
devtoolset-7对应gcc7.x.x版本
devtoolset-8对应gcc8.x.x版本
devtoolset-9对应gcc9.x.x版本



### 安装步骤

由于devtoolset-3版本太老官方仓库已经不再提供安装，所以从需要先从其他网站更新仓库地址，具体更新和安装步骤如下

```shell
cd /etc/yum.repos.d

wget https://copr.fedorainfracloud.org/coprs/rhscl/devtoolset-3-el7/repo/epel-7/rhscl-devtoolset-3-el7-epel-7.repo

yum install devtoolset-3-toolchain

scl enable devtoolset-3 bash

gcc -v

Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/opt/rh/devtoolset-3/root/usr/libexec/gcc/x86_64-redhat-linux/4.9.2/lto-wrapper
Target: x86_64-redhat-linux
Configured with: ../configure --prefix=/opt/rh/devtoolset-3/root/usr --mandir=/opt/rh/devtoolset-3/root/usr/share/man --infodir=/opt/rh/devtoolset-3/root/usr/share/info --with-bugurl=http://bugzilla.redhat.com/bugzilla --enable-bootstrap --enable-shared --enable-threads=posix --enable-checking=release --enable-multilib --with-system-zlib --enable-__cxa_atexit --disable-libunwind-exceptions --enable-gnu-unique-object --enable-linker-build-id --enable-languages=c,c++,fortran,lto --enable-plugin --with-linker-hash-style=gnu --enable-initfini-array --disable-libgcj --with-isl=/builddir/build/BUILD/gcc-4.9.2-20150212/obj-x86_64-redhat-linux/isl-install --with-cloog=/builddir/build/BUILD/gcc-4.9.2-20150212/obj-x86_64-redhat-linux/cloog-install --enable-gnu-indirect-function --with-tune=generic --with-arch_32=i686 --build=x86_64-redhat-linux
Thread model: posix
gcc version 4.9.2 20150212 (Red Hat 4.9.2-6) (GCC)
```

### 参考

https://linux.web.cern.ch/devtoolset/

https://copr.fedorainfracloud.org/coprs/rhscl/devtoolset-3-el7/

https://blog.csdn.net/zzhongcy/article/details/89950976