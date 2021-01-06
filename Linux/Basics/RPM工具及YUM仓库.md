<!-- toc -->
[toc]
<!-- toc -->



# RPM

> RPM是世界著名的Red Hat公司推出的一种软件包安装工具，全称为Redhat Package Manager  
> rpm命令包含了五种基本功能：安装、卸载、升级、查询和验证  

**rpm [参数] [软件包]**

常用命令


``` bash
rpm -qa <package.rpm>     # 列出所有被安装的rpm package
rpm -q <package.rpm>      # 查询包是否被安装 
rpm -qi <package.rpm>     # 得到被安装的包的信息
rpm -ql <package.rpm>     # 列出该包中有哪些文件
rpm -qf 文件名称     # 列出服务器上的一个文件属于哪个rpm包
```

示例：

``` bash
rpm -qa |grep rsync # 软件是否安装
```

``` bash
rpm -ivh <package.rpm>    # 安装一个包
rpm --force -ivh <package.rpm>    #强制安装
 	--force  # 即使覆盖属于其它包的文件也强迫安装
	--nodeps # 如果该RPM包的安装依赖其它包，即使其它包没装，也强迫安装。
rpm -Uvh <package.rpm>    #升级包
rpm -e <package.rpm>      #移除包
```



| -a            | 查询所有的软件包                                 |
| ------------- | ------------------------------------------------ |
| -b或-t        | 设置包装套件的完成阶段，并指定套件档的文件名称； |
| -c            | 只列出组态配置文件，本参数需配合”-l”参数使用     |
| -d            | 只列出文本文件，本参数需配合”-l”参数使用         |
| -e或--erase   | 卸载软件包                                       |
| -f            | 查询文件或命令属于哪个软件包                     |
| -h或--hash    | 安装软件包时列出标记                             |
| -i            | 显示软件包的相关信息                             |
| --install     | 安装软件包                                       |
| -l            | 显示软件包的文件列表                             |
| -p            | 查询指定的rpm软件包                              |
| -q            | 查询软件包                                       |
| -R            | 显示软件包的依赖关系                             |
| -s            | 显示文件状态，本参数需配合”-l”参数使用           |
| -U或--upgrade | 升级软件包                                       |
| -v            | 显示命令执行过程                                 |
| -vv           | 详细显示指令执行过程                             |



# Yum

## yum 安装软件步骤

> 建立本地yum源
>
> 清除yum缓存 yum clean all
>
> 重新生成yum缓存 yum makecache all
>
> 安装软件 yum install <packagename>
>
> 卸载软件 yum remove <packagename>

## **yum 常用命令**

~~~
yum install package    #安装rpm包
yum remove package     #卸载rpm包
yum update package     #跟新包
yum check-update       #检查可更新包
yum list               #显示包列表
yum list installed | grep openssh-server    # 查看 openssh-server是否 安装
~~~



## 更改yum源配置

## **配置yum为阿里云源**

``` bash
cd /etc/yum.repos.d/
mv CentOS-Base.repo CentOS-Base.repo.back  # 备份`CentOS-Base.repo` yum网络源的配置文件

wget -O CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

yum clean all
yum makecache
```



## **配置为本地源**

`vim My-Yum.repo`创建新的配置文件，后缀为`.repo`的文件

或者更改此文件`vim /etc/yum.repos.d/CentOS-Media.repo`

``` bash
[myyum-media]               #Yum软件仓库唯一标识符，避免与其他仓库冲突。
name=my-centos-yum          #Yum软件仓库的名称描述，易于识别仓库用处。
# 提供的方式包括(ftp://,http://,本地 file:/// )
baseurl=file:///media/cdrom/       #本地光盘文件，必填，可以有多个
       =http://mirrors.163.com/centos/$releasever/os/$basearch/   
       =ftp:// 
enabled=1                   #设置此源是否可用；1为可用，0为禁用。
gpgcheck=1                  #设置此源是否校验文件；1为校验，0为不校验。
gpgkey=file:///media/cdrom/RPM-GPG-KEY-redhat-release   #若上面参数开启校验，那么请指定公钥文件地址。
```

按配置参数的路径挂载光盘,并将光盘挂在信息写到/etc/fstab文件中

``` bash
[root@centos1 yum.repos.d]# vim /etc/fstab
/dev/cdrom /media/cdrom iso9660 defaults 0 0
```

``` shell
mkdir -p /media/cdrom  
mount /dev/cdrom /media/cdrom/  #可以先挂载系统安装盘，进行本地yum源配置
```

4. `yum install httpd -y` 检查Yum软件仓库是否已经可用


    name=CentOS-$releasever - Media
    
    #支持变量
    $releasever：代表发行版的版本，从[main]部分的distroverpkg获取，如果没有，则根据redhat-release包进行判断。  
    $arch：cpu体系，如i686,athlon等  
    $basearch：cpu的基本体系组，如i686和athlon同属i386，alpha和alphaev6同属alpha。
    
    网易CentOS yum镜像使用帮助 http://mirrors.163.com/.help/centos.html
    
    国内开源镜像站：    
    - 网易：http://mirrors.163.com/  
    - 阿里：https://developer.aliyun.com/mirror/
    - 清华大学：https://mirror.tuna.tsinghua.edu.cn/
    - 浙江大学：http://mirrors.zju.edu.cn/   
    - 北京理工大学：http://mirror.bit.edu.cn
    - 搜狐：http://mirrors.sohu.com/   



# 源码安装

追加--prefix参数，以指定稍后源码包程序的安装路径





**第1步**：下载及解压源码包文件。为了方便在网络中传输，源码包文件通常会在归档后使用gzip或bzip2等格式进行压缩，因此一般会具有.tar.gz与.tar.bz2的后缀。要想使用源码包安装服务程序，必须先把里面的内容解压出来，然后再切换到源码包文件的目录中：

> [root@linuxprobe ~]# tar xzvf FileName**.tar.gz**
>
> [root@linuxprobe ~]# cd FileDirectory

**第2步**：编译源码包代码。在正式使用源码包安装服务程序之前，还需要使用编译[脚本](https://www.linuxcool.com/)针对当前系统进行一系列的评估工作，包括对源码包文件、软件之间及函数库之间的依赖关系、编译器、汇编器及连接器进行检查。我们还可以根据需要来追加--prefix参数，以指定稍后源码包程序的安装路径，从而对服务程序的安装过程更加可控。当编译工作结束后，如果系统环境符合安装要求，一般会自动在当前目录下生成一个Makefile安装文件。

> [root@linuxprobe ~]# ./configure --prefix=/usr/local/program

**第3步**：生成二进制安装程序。刚刚生成的Makefile文件中会保存有关系统环境、软件依赖关系和安装规则等内容，接下来便可以使用make[命令](https://www.linuxcool.com/)来根据Makefile文件内容提供的合适规则编译生成出真正可供用户安装服务程序的二进制可执行文件了。

> [root@linuxprobe ~]# make

**第4步**：运行二进制的服务程序安装包。由于不需要再检查系统环境，也不需要再编译代码，因此运行二进制的服务程序安装包应该是速度最快的步骤。如果在源码包编译阶段使用了--prefix参数，那么此时服务程序就会被安装到那个目录，如果没有自行使用参数定义目录的话，一般会被默认安装到/usr/local/bin目录中。

> [root@linuxprobe ~]# make install

**第5步**：清理源码包临时文件。由于在安装服务程序的过程中进行了代码编译的工作，因此在安装后目录中会遗留下很多临时垃圾文件，本着尽量不要浪费磁盘存储空间的原则，可以使用make clean[命令](https://www.linuxcool.com/)对临时文件进行彻底的清理工作。

> [root@linuxprobe ~]# make clean











# apt-get 安装

``` bash
apt-get install package     # 安装软件
apt-get remove package      # 删除软件
apt-get purge package       # 连配置一起删除
apt-cache search nurse|less  # 搜索包
```

