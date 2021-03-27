---
title: 记一次samba服务器漏洞升级维护
date: 2021-01-11 20:00:35
tags: Linux
categories: 环境配置
---

实验室服务器太久没维护，samba的一个漏洞被机房封IP了，记录一下修复的过程。

服务器系统为CentOS 7.8，是实验室的文件存储中心。修复的难度主要来源于每行代码要检查N遍才敢回车，生怕自己不小心删库，然后快进到退学提桶跑路。

> 后续：机房禁止samba服务，改用FTP了。。。

<!-- more -->

## 查看当前安装的Samba服务器版本

```bash
smbstatus
```

当前是4.10.13

## 查看yum源里面的Samba版本

```bash
yum list | grep samba
# 输出 samba.x86_64                                3:4.10.13-1.el7            @copr:copr.fedorainfracloud.org:sergiomb:SambaAD
```

发现yum上面的版本和本地一样,应该是不能yum update samba了。。。只能源码安装

## 备份Samba的信息

### 备份samba存的的数据

- 看一下smb文件夹有多大，目前是560多G

  ```bash
  cd /home
  du -h --max-depth=1
  ```
  
- 看下当前系统还够不够放,发现/home目录下面18T的空间可用，所以就tar备份在这里

  ```bash
  df -h
  ```

- 备份/home/smb文件夹

  ```bash
  mkdir smb_backup 
  cd smb_backup
  tar -jpcv -f 20200107_smb_backup.tar.bz2 /home/smb/
  ```

  上面的命令解释：-j为采用bzip2压缩; -p为保持文件权限; -c创建压缩; -v可视化压缩过程; -f后面跟着压缩文件的命令，也就是把/home/smb/文件夹压缩打包到20200107_smb_backup.tar.bz2这个文件

  > 注意：本次备份没有加-j参数，也就是不压缩只打包，因为-j太慢了。。。反正现在空间还很多

### 备份Samba配置文件

- 先查看当前状态

  ```bash
  service smb status 
  # 显示 Active: active (running)
  ```
  
 - 停止服务 

   ```bash
   service smb stop
   service smb status 
   # 显示 Active: inactive (dead)
   ```

- 备份

  ```bash
  smbd -b | grep DIR
  # 输出 PRIVATE_DIR: /var/lib/samba/private
  cp /var/lib/samba/private /home/smb_backup/20200107 #samba数据库
  cp /etc/samba/smb.conf /home/smb_backup/20200107 #samba主要配置文件
  ```

## 源码编译

> 参考[官网文档](https://wiki.samba.org/index.php/Build_Samba_from_Source)和官网[依赖说明](https://wiki.samba.org/index.php/Package_Dependencies_Required_to_Build_Samba#Red_Hat_Enterprise_Linux_7_.2F_CentOS_7_.2F_Scientific_Linux_7)

- 下载最新的官网stable源码（截止20210107）

  ```bash
  wget https://download.samba.org/pub/samba/stable/samba-4.13.3.tar.gz
  tar -zxf samba-4.13.3.tar.gz
  ```

- 安装依赖

  ```bash
  yum install attr bind-utils docbook-style-xsl gcc gdb krb5-workstation \
         libsemanage-python libxslt perl perl-ExtUtils-MakeMaker \
         perl-Parse-Yapp perl-Test-Base pkgconfig policycoreutils-python \
         python2-crypto gnutls-devel libattr-devel keyutils-libs-devel \
         libacl-devel libaio-devel libblkid-devel libxml2-devel openldap-devel \
         pam-devel popt-devel python-devel readline-devel zlib-devel systemd-devel \
         lmdb-devel jansson-devel gpgme-devel pygpgme libarchive-devel
  ```

  不出所料这样是失败的。。。错误信息

  ```
  Transaction check error:
    file /usr/lib64/libgnutlsxx.so.28.1.0 from install of gnutls-c++-3.3.29-9.el7_6.x86_64 conflicts with file from package compat-gnutls34-c++-3.4.17-6.el7.x86_64
    file /usr/lib64/libhogweed.so from install of nettle-devel-2.7.1-8.el7.x86_64 conflicts with file from package compat-nettle32-devel-3.2-3.el7.x86_64
    file /usr/lib64/libnettle.so from install of nettle-devel-2.7.1-8.el7.x86_64 conflicts with file from package compat-nettle32-devel-3.2-3.el7.x86_64
    file /usr/lib64/libgnutls-dane.so.0 from install of gnutls-dane-3.3.29-9.el7_6.x86_64 conflicts with file from package compat-gnutls34-dane-3.4.17-6.el7.x86_64
    file /usr/lib64/libgnutls-dane.so from install of gnutls-devel-3.3.29-9.el7_6.x86_64 conflicts with file from package compat-gnutls34-devel-3.4.17-6.el7.x86_64
    file /usr/lib64/libgnutls.so from install of gnutls-devel-3.3.29-9.el7_6.x86_64 conflicts with file from package compat-gnutls34-devel-3.4.17-6.el7.x86_64
  ```

  官网表示，红帽系列发行版7系列都没有满足的gnutls包

  > Red Hat Enterprise Linux 7 and deritivites do not provide a GnuTLS version >= 3.4.7, even when EPEL is used. Users building Samba 4.12 will need to obtain GnuTLS from outside RHEL7 / CentOS7 / EPEL to use Samba 4.12.

  所以第三方安装``gnutls``这个包

- 安装``gnutls``

  > 目前官网最新的是3.6.15这个包，经过测试编译不通过，应该是和openssl版本有关系（当前为OpenSSL 1.1.1g），每次编译到src文件夹下面的时候就会报找不到openssl的某个函数的错误，因此用老版本的**3.6.4**，切记！

  ```bash
  mkdir 3rdpary
  cd 3rdparty
  wget https://www.gnupg.org/ftp/gcrypt/gnutls/v3.6/gnutls-3.6.4.tar.xz
  tar -xvJf gnutls-3.6.4.tar.xz
  ./configure  --without-p11-kit 
  ```

  然后又出错了。。。

  ```bash
  configure: error: 
    ***
    *** Libnettle 3.4.1 was not found.
  ```

- 安装``Libnettle 3.4.1``

  ```bash
  wget https://ftp.gnu.org/gnu/nettle/nettle-3.4.1.tar.gz
  tar -zxf nettle-3.4.1.tar.gz
  ./configure
  make
  ```

  make的时候又出错。。。我TM。。。

  ```
  rsa-sign-tr.c: 在函数‘sec_equal’中:
  rsa-sign-tr.c:243:3: 错误：只允许在 C99 模式下使用‘for’循环初始化声明
     for (size_t i = 0; i < limbs; i++)
     ^
  rsa-sign-tr.c:243:3: 附注：使用 -std=c99 或 -std=gnu99 来编译您的代码
  make[1]: *** [rsa-sign-tr.o] 错误 1
  make[1]: 离开目录“/home/smb_backup/samba_src/3rdparty/nettle-3.4.1”
  make: *** [all] 错误 2
  ```

  大概意思是make有问题

  ```bash
  ./configure --disable-openssl --prefix=/usr/ 
  #上面两个追加参数很关键
  #disable-openssl解决不编译到example文件夹的时候找不到openssl，直接跳过拉倒
  #prefix解决后续找不到库的问题，默认安装是装在/usr/local/lib64下面，系统在/usr/lib64找
  vim config.make
  # 在CFLAGS一行后面加上-std=c99
  make
  make install
  ```
  
  libnettle**安装好了**
  
- 继续安装``gnutls``

  ```bash
  ./configure  --without-p11-kit 
  # 还是失败
    configure: error: 
      ***
      *** Libunistring was not found. To use the included one, use --with-included-unistring
  #安装Libunistring
  yum install libunistring-devel
  ./configure  --without-p11-kit
  make
  make install
  ```

  **成功**安装gnults

- 安装samba

  即使安装了gnults，因为安装路径不在/usr/lib64，系统找不到，这里懒得重新编译安装了。。。直接软连接过去

  ```bash
  ln -s /usr/local/lib/pkgconfig/gnutls.pc /usr/lib64/pkgconfig/gnutls.pc
  # ln -sf会覆盖，先备份一下原来的
  cp /usr/lib64/libgnutls.so /usr/lib64/libgnutls.so.bak
  ln -sf /usr/local/lib/libgnutls.so /usr/lib64/libgnutls.so
  cp /usr/lib64/libgnutls.so.30 /usr/lib64/libgnutls.so.30.bak
  ln -sf /usr/local/lib/libgnutls.so.30 /usr/lib64/libgnutls.so.30
  ```

  再尝试编译

  ```bash
  ./configure --disable-python --without-ad-dc --without-json --without-libarchive  --without-acl-support 
  make
  # 输出 'build' finished successfully (12m15.538s)
  make install 
  # 输出 'install' finished successfully (3m9.860s)
  ```

  ！！！**成功了**

  现在samba安装在/usr/local/samba文件夹下面

## 配置新的Samba，关掉旧的

```bash
ln -s /usr/local/samba/etc/smb.conf /usr/local/samba/lib/smb.conf
touch /etc/ld.so.conf.d/samba.conf
vim /etc/ld.so.conf.d/samba.conf
# 添加/usr/local/samba/lib
ldconfig
touch /etc/profile.d/samba.sh
vim /etc/profile.d/samba.sh
# 添加 export PATH=$PATH:/usr/local/samba/bin:/usr/local/samba/sbin

```

由于在``yum remove samba``命令会卸载太多的依赖，在这台资料服务器上不敢瞎搞，尝试着把/usr/local/samba/sbin的四个文件都ln -sf到了/usr/sbin
然后重启电脑，打开/usr/local/samba/var查看smbd的log文件，发现最新的日志是
```
[2021/01/08 21:05:14.847119,  0] ../../source3/smbd/server.c:1784(main)
  smbd version 4.13.3 started.
  Copyright Andrew Tridgell and the Samba Team 1992-2020
```

## 后续

貌似没啥问题了，机房网管表示扫不到漏洞了，数据也都在，各个用户的权限也不用重新配置。

