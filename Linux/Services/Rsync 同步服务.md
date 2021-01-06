<!-- toc -->
[toc]
<!-- toc -->



参考： https://clsn.io/clsn/lx920.html



https://www.cnblogs.com/kevingrace/category/924886.html

https://www.jianshu.com/p/d32414af2001



https://www.centos.bz/2018/11/centos7%e4%b8%8brsynccrontab%e5%ae%9a%e6%9c%9f%e5%90%8c%e6%ad%a5%e5%a4%87%e4%bb%bd/





# Rsync简介

https://www.cnblogs.com/noxy/p/8986164.html

rsync 具有如下的基本特性：

1. 可以镜像保存整个目录树和文件系统
2. 可以很容易做到保持原来文件的权限、时间、软硬链接等
3. 无须特殊权限即可安装
4. 优化的流程，文件传输效率高
5. 可以使用 rsh、ssh 方式来传输文件，当然也可以通过直接的 socket 连接
6. 支持匿名传输，以方便进行网站镜象

远程 Shell 方式（建议使用 ssh，用户验证由 ssh 负责）和 C/S 方式（即客户连接远程 rsync 服务器，用户验证由 rsync 服务器负责）。
无论本地同步目录还是远程同步数据，首次运行时将会把全部文件拷贝一次，以后再运行时将只拷贝有变化的文件（对于新文件）或文件的变化部分（对于原有文件）



```
01. 支持拷贝普通文件与特殊文件如链接文件，设备等。
02. 可以有排除指定文件或目录同步的功能，相当于打包命令tar的排除功能。
    #tar zcvf backup_1.tar.gz  /opt/data  -exclude=clsn   
    说明：在打包/opt/data时就排除了clsn命名的目录和文件。
03. 可以做到保持原文件或目录的权限、时间、软硬链接、属主、组等所有属性均不改变-p。
04. 可实现增量同步，既只同步发生变化的数据，因此数据传输效率很高（tar -N）。
    # 将备份/home 目录自 2008-01-29 以来修改过的文件
    # tar -N 2008-01-29 -zcvf /backups/inc-backup_$(date +%F).tar.gz /home
    # 将备份 /home 目录昨天以来修改过的文件
    # tar -N $(date -d yesterday "+%F") -zcvf /backups/inc-backup_$(date +%F).tar.gz /home
    # 添加文件到已经打包的文件
    # tar -rf all.tar *.gif
    说明：这条命令是将所有.gif的文件增加到all.tar的包里面去。-r是表示增加文件的意思。
05. 可以使用rcp,rsh,ssh等方式来配合进行隧道加密传输文件（rsync本身不对数据加密）
06. 可以通过socket(进程方式)传输文件和数据（服务端和客户端）*****。重点掌握
07. 支持匿名的或认证（无需系统用户）的进程模式传输，可实现方便安全的进行数据备份及镜像。
```





## 安装

网址：https://rsync.samba.org/

安装依赖帮助：https://github.com/WayneD/rsync/blob/master/INSTALL.md

``` bash
sudo yum -y install epel-release
sudo yum -y install gcc g++ gawk autoconf automake python3-pip
sudo yum -y install acl libacl-devel
sudo yum -y install attr libattr-devel
sudo yum -y install xxhash-devel
sudo yum -y install libzstd-devel
sudo yum -y install lz4-devel
sudo yum -y install openssl-devel
pip3 install --user commonmark
```





``` bash
cd /usr/local/src
wget https://rsync.samba.org/ftp/rsync/src/rsync-3.2.0.tar.gz

tar zxf  rsync-3.2.0.tar.gz && cd rsync-3.2.0 && ./configure --prefix=/usr/local/rsync

```



## 功能介绍

**类似于 cp，本地备份传输数据**

``` bash
➜  [/root] rsync /home/lxj/index.html /root
```

**类似于scp，远程备份传输数据**
``` bash
➜  [/root] rsync -rp /root/index.html 192.168.44.130:/root/test/
root@192.168.44.130's password:
```

**类似于rm  实现无差异同步备份**
将test2文件夹中所有内容备份到 test目录下

``` bash
➜  [/root] rsync -a --delete /root/test2 /root/test
➜  [/root] l test
total 8.0K
drwxr-xr-x.  3 root root   37 Jan  4 17:48 .
dr-xr-x---. 10 root root 4.0K Jan  4 17:48 ..
-rw-r--r--.  1 root root   25 Jan  4 17:42 index.html
drwxr-xr-x.  2 root root   22 Jan  4 17:47 test2
```
**类似于ls 本地文件查看**
``` bash
➜  [/root] rsync index.html
-rw-r--r--             25 2021/01/04 16:56:43 index.html

# 远程查看文件
➜  [/root] rsync 192.168.44.130:/root/test/
root@192.168.44.130's password:
-rw-r--r--             25 2021/01/04 17:38:22 index.html
```



## Rsync的企业工作场景说明

> 1. 两台服务器之间数据同步（定时任务cron+rsync）
>
>   同步网站内部人员数据信息（定时任务最小周期为1分钟）
>
> 2. 两台服务器之间数据同步（实时任务inotify/sersync/lrsyncd+rsync）
>
>   同步网站用户人员数据信息













# 远程数据同步方式（类似SCP）

**push推到远程,将本地数据，推送到远程**

``` bash
rsync [option] src [user@]host:dest

# 将本地/root/test目录及目录本身推送到远程
➜  [/root] rsync -r /root/test lxj@192.168.44.130:/home/lxj
```

> /test  --将test目录本身及目录下的内容进行传输
> /test/ --只传输test目录下面的内容信息

**将从远程拉取pull,将远程数据拉取到本地**

``` bash
rsync [option] [user@]host:src [dest] 

#将远端index.html文件拉取到本地目录
➜  [/root] rsync lxj@192.168.44.130:/home/lxj/test/index.html /root
```



## 守护进程方式同步数据

### 服务器

创建rsync用户

```
 useradd -s /sbin/nologin -M rsync
```

创建数据备份储存目录,目录修改属主

```
[root@backup ~]# mkdir /backup/
[root@backup ~]# chown -R rsync.rsync /backup/
```

 创建认证用户密码文件

```
echo "rsync_backup:clsn123" >>/etc/rsync.password
chmod 600 /etc/rsync.password
```

启动rsync服

```
rsync --daemon
```

服务端配置完成

```
[root@backup ~]# ps -ef |grep rsync

[root@backup ~]# netstat -lntup |grep rsync
```



### 客户端

创建认证文件

  客户端的认证文件只需要有密码即可

```
echo "clsn123" >>/etc/rsync.password
chmod 600 /etc/rsync.password
```

实现数据传输

交互式

```bash
[root@nfs01 ~]# rsync -avzP /etc/hosts rsync_backup@172.16.1.41::backup
Password:
sending incremental file list
hosts
         357 100%    0.00kB/s    0:00:00 (xfer#1, to-check=0/1)
 
sent 63 bytes  received 33 bytes  9.14 bytes/sec
total size is 357  speedup is 3.72
```

免交互式

```bash
[root@nfs01 ~]# rsync -avzP /etc/hosts rsync_backup@172.16.1.41::backup --password-file=/etc/rsync.password
sending incremental file list
hosts
         357 100%    0.00kB/s    0:00:00 (xfer#1, to-check=0/1)
 
sent 199 bytes  received 27 bytes  150.67 bytes/sec
total size is 357  speedup is 1.58
```





# 配置文件

https://clsn.io/clsn/lx920.html#3_rsycn_rsyncdconf

``` bash
➜  [/root] vim /etc/rsyncd.conf   # 没有则手动创建

man rsyncd.conf
```







# [**rsync + inotify 实时同步**](https://www.cnblogs.com/hukey/p/10729218.html)