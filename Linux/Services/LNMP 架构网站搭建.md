<!-- toc -->
[toc]
<!-- toc -->





LNMP 自动安装脚本https://lnmp.org/download.html

见桌面

LNMP动态网站部署架构是一套由Linux + Nginx + MySQL + PHP组成的动态网站系统解决方案

**需要安装如下依赖**

``` bash
[root@centos1 ~]# yum install -y apr* autoconf automake bison bzip2 bzip2* compat* cpp curl curl-devel fontconfig fontconfig-devel freetype freetype* freetype-devel gcc gcc-c++ gd gettext gettext-devel glibc kernel kernel-headers keyutils keyutils-libs-devel krb5-devel libcom_err-devel libpng libpng-devel libjpeg* libsepol-devel libselinux-devel libstdc++-devel libtool* libgomp libxml2 libxml2-devel libXpm* libtiff libtiff* make mpfr ncurses* ntp openssl openssl-devel patch pcre-devel perl php-common php-gd policycoreutils telnet t1lib t1lib* nasm nasm* wget zlib-devel
```











#### 以下为书中命令

yum 安装，一条命令搞定

``` bash
yum install httpd httd-devel mysql-server mysql-devel php pgp-devel php-mysql -y
```





源码安装

``` bash
# Apache
yum install apr-devel apr-util-devel -y
cd /usr/src
wget http://mirror.bit

```





