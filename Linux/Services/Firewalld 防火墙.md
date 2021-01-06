<!-- toc -->
[toc]
<!-- toc -->



iptables和firewalld的介绍与区别   https://blog.csdn.net/Bilise/article/details/105372667





https://www.jianshu.com/p/cbf8e0009bc0

https://www.linuxprobe.com/chapter-10.html 10.3



https://access.redhat.com/documentation/zh-cn/red_hat_enterprise_linux/7/html/security_guide/sec-using_firewalls#sec-Comparison_of_Firewalld_to_system-config-firewall_and_iptables



# Firewalld 防火墙

防火墙策略可以基于流量的源目地址、端口号、协议、应用等信息来定制

> iptables服务会把配置好的防火墙策略交由内核层面的netfilter网络过滤器来处理
> firewalld服务则是把配置好的防火墙策略交由内核层面的nftables包过滤框架来处理



> firewalld的配置方法主要有三种：firewall-config、firewall-cmd和直接编辑xml文件，其中 firewall-config是图形化工具，firewall-cmd是命令行工具

**查看端口被哪个进程占用 netstat -tunlp|grep 8080**





## Firewalld 常用命令

``` bash
yum install firewalld firewall-config   # 安装

systemctl start  firewalld   # 开启防火墙

systemctl restart firewalld 
 
systemctl stop firewalld     # 关闭

systemctl status firewalld 或者 firewall-cmd --state   # 查看防火墙状态

systemctl enable firewalld.service         #开机启用服务

systemctl disable firewalld.service        #开机禁用服务

systemctl is-enabled firewalld.service     #查看服务是否开机启动
systemctl --failed                         #查看启动失败的服务列表
systemctl list-unit-files|grep enabled     #查看已启动的服务列表
systemctl list-unit-files | grep firewalld.service   # 查看是否开机启动

systemctl mask firewalld    #执行命令，即可实现取消服务的锁定

systemctl unmask firewalld   # 下次需要锁定该服务时执行
```



## 配置



系统配置目录/usr/lib/firewalld/services 目录中存放定义好的网络服务和端口参数，系统参数，不能修改

用户配置目录/etc/firewalld/



## firewall-cmd 工具



``` bash
firewall-cmd --state #查看防火墙状态
firewall-cmd --reload #更新防火墙规则
firewall-cmd --state #查看防火墙状态
firewall-cmd --reload #重载防火墙规则
firewall-cmd --list-ports #查看所有打开的端口
firewall-cmd --list-services #查看所有允许的服务
firewall-cmd --get-services #获取所有支持的服务

#区域相关
firewall-cmd --list-all-zones #查看所有区域信息
firewall-cmd --get-active-zones #查看活动区域信息
firewall-cmd --set-default-zone=public #设置public为默认区域
firewall-cmd --get-default-zone #查看默认区域信息
firewall-cmd --zone=public --add-interface=eth0 #将接口eth0加入区域public

#接口相关
firewall-cmd --zone=public --remove-interface=eth0 #从区域public中删除接口eth0
firewall-cmd --zone=default --change-interface=eth0 #修改接口eth0所属区域为default
firewall-cmd --get-zone-of-interface=eth0 #查看接口eth0所属区域

#端口控制
firewall-cmd --add-port=80/tcp --permanent #永久添加80端口例外(全局)
firewall-cmd --remove-port=80/tcp --permanent #永久删除80端口例外(全局)
firewall-cmd --add-port=65001-65010/tcp --permanent #永久增加65001-65010例外(全局)
firewall-cmd --zone=public --add-port=80/tcp --permanent #永久添加80端口例外(区域public)
firewall-cmd --zone=public --remove-port=80/tcp --permanent #永久删除80端口例外(区域public)
firewall-cmd --zone=public --add-port=65001-65010/tcp --permanent #永久增加65001-65010例外(区域public)
firewall-cmd --query-port=8080/tcp # 查询端口是否开放
firewall-cmd --permanent --add-port=80/tcp # 开放80端口
firewall-cmd --permanent --remove-port=8080/tcp # 移除端口
firewall-cmd --reload #重启防火墙(修改配置后要重启防火墙)
```





``` bash
显示状态：$ firewall-cmd --state

查看区域信息: $ firewall-cmd --get-active-zones

查看指定接口所属区域：$ firewall-cmd --get-zone-of-interface=eth0

拒绝所有包：# firewall-cmd --panic-on

取消拒绝状态：# firewall-cmd --panic-off

查看是否拒绝：$ firewall-cmd --query-panic

 

更新防火墙规则：# firewall-cmd --reload

          # firewall-cmd --complete-reload

    两者的区别就是第一个无需断开连接，就是firewalld特性之一动态添加规则，第二个需要断开连接，类似重启服务

 

将接口添加到区域，默认接口都在public

# firewall-cmd --zone=public --add-interface=eth0

永久生效再加上 --permanent 然后reload防火墙

 

设置默认接口区域

# firewall-cmd --set-default-zone=public

立即生效无需重启

 

打开端口（貌似这个才最常用）

查看所有打开的端口：

# firewall-cmd --zone=dmz --list-ports

加入一个端口到区域：

# firewall-cmd --zone=dmz --add-port=8080/tcp

若要永久生效方法同上

 

打开一个服务，类似于将端口可视化，服务需要在配置文件中添加，/etc/firewalld 目录下有services文件夹，这个不详细说了，详情参考文档

# firewall-cmd --zone=work --add-service=smtp

 

移除服务

# firewall-cmd --zone=work --remove-service=smtp
```

``` bash




```





# Iptables



``` bash
yum install iptables-services #安装iptables
systemctl stop firewalld.service #停止firewalld
systemctl mask firewalld.service #禁止自动和手动启动firewalld
systemctl start iptables.service #启动iptables
systemctl start ip6tables.service #启动ip6tables
systemctl enable iptables.service #设置iptables自启动
systemctl enable ip6tables.service #设置ip6tables自启动
```





``` bash
iptables 基本命令使用举例

一、链及NAT的基本操作
1、清除所有的规则。
1）清除预设表filter中所有规则链中的规则。
# iptables -F
2）清除预设表filter中使用者自定链中的规则。
#iptables -X

#iptables -Z

3)清楚NAT表规则

#iptables -F -t nat

4)NAT表的显示

#iptables -t nat -nL

 

2、设置链的默认策略。一般有两种方法。
1）首先允许所有的包，然后再禁止有危险的包通过放火墙。
#iptables -P INPUT ACCEPT
#iptables -P OUTPUT ACCEPT
#iptables -P FORWARD ACCEPT
2）首先禁止所有的包，然后根据需要的服务允许特定的包通过防火墙。
#iptables -P INPUT DROP
#iptables -P OUTPUT DROP
#iptables -P FORWARD DROP
3、列出表/链中的所有规则。默认只列出filter表。
#iptables -L
4、向链中添加规则。下面的语句用于开放网络接口：
#iptables -A INPUT -i lo -j ACCEPT
#iptables -A OUTPUT -o lo -j ACCEPT
#iptables -A INPUT -i eth0 -j ACEPT
#iptables -A OUTPUT -o eth1 -j ACCEPT
#iptables -A FORWARD -i eth1 -j ACCEPT
#iptables -A FORWARD -0 eth1 -j ACCEPT
注意:由于本地进程不会经过FORWARD链，因此回环接口lo只在INPUT和OUTPUT两个链上作用。
5、使用者自定义链。
#iptables -N custom
#iptables -A custom -s 0/0 -d 0/0 -p icmp -j DROP
#iptables -A INPUT -s 0/0 -d 0/0 -j DROP
二、设置基本的规则匹配
1、指定协议匹配。
1）匹配指定协议。
#iptables -A INPUT -p tcp
2）匹配指定协议之外的所有协议。
#iptables -A INPUT -p !tcp
2、指定地址匹配。
1）指定匹配的主机。
#iptables -A INPUT -s 192.168.0.18
2）指定匹配的网络。
#iptables -A INPUT -s 192.168.2.0/24
3）匹配指定主机之外的地址。
#iptables -A FORWARD -s !192.168.0.19
4）匹配指定网络之外的网络。
#iptables -A FORWARD -s ! 192.168.3.0/24
3、指定网络接口匹配。
1）指定单一的网络接口匹配。
#iptables -A INPUT -i eth0
#iptables -A FORWARD -o eth0
2）指定同类型的网络接口匹配。
#iptables -A FORWARD -o ppp+
4、指定端口匹配。
1）指定单一端口匹配。
#iptables -A INPUT -p tcp --sport www
#iptables -A INPUT -p udp –dport 53
2）匹配指定端口之外的端口。
#iptables -A INPUT -p tcp –dport !22
3）匹配端口范围。
#iptables -A INPUT -p tcp –sport 22:80
4）匹配ICMP端口和ICMP类型。
#iptables -A INOUT -p icmp –icimp-type 8
5）指定ip碎片。
每
个网络接口都有一个MTU（最大传输单元），这个参数定义了可以通过的数据包的最大尺寸。如果一个数据包大于这个参数值时，系统会将其划分成更小的数据包
（称为ip碎片）来传输，而接受方则对这些ip碎片再进行重组以还原整个包。这样会导致一个问题：当系统将大数据包划分成ip碎片传输时，第一个碎片含有
完整的包头信息（IP+TCP、UDP和ICMP），但是后续的碎片只有包头的部分信息（如源地址、目的地址）。因此，检查后面的ip碎片的头部（象有
TCP、UDP和ICMP一样）是不可能的。假如有这样的一条规则：
#iptables -A FORWARD -p tcp -s 192.168.1.0/24 -d 192.168.2.100 –dport 80 -j ACCEPT
并且这时的FORWARD的policy为DROP时，系统只会让第一个ip碎片通过，而余下的碎片因为包头信息不完整而无法通过。可以通过—fragment/-f 选项来指定第二个及以后的ip碎片解决上述问题。
#iptables -A FORWARD -f -s 192.168.1.0/24 -d 192.168.2.100 -j ACCEPT
注意现在有许多进行ip碎片攻击的实例，如DoS攻击，因此允许ip碎片通过是有安全隐患的，对于这一点可以采用iptables的匹配扩展来进行限制。
三、设置扩展的规则匹配（举例已忽略目标动作）
1、多端口匹配。
1）匹配多个源端口。
#iptables -A INPUT -p tcp -m multiport –sport 22,53,80,110
2）匹配多个目的端口。
#iptables -A INPUT -p tcp -m multiport –dpoort 22,53,80
3）匹配多端口(无论是源端口还是目的端口）
#iptables -A INPUT -p tcp -m multiport –port 22,53,80,110
2、指定TCP匹配扩展
使用 –tcp-flags 选项可以根据tcp包的标志位进行过滤。
#iptables -A INPUT -p tcp –tcp-flags SYN,FIN,ACK SYN
#iptables -A FROWARD -p tcp –tcp-flags ALL SYN,ACK
上实例中第一个表示SYN、ACK、FIN的标志都检查，但是只有SYN匹配。第二个表示ALL（SYN，ACK，FIN，RST，URG，PSH）的标志都检查，但是只有设置了SYN和ACK的匹配。
#iptables -A FORWARD -p tcp --syn
选项—syn相当于”--tcp-flags SYN,RST,ACK SYN”的简写。
3、limit速率匹配扩展。
1）指定单位时间内允许通过的数据包个数，单位时间可以是/second、/minute、/hour、/day或使用第一个子母。
#iptables -A INPUT -m limit --limit 300/hour
2 )指定触发事件的阀值。
#iptables -A INPUT -m limit –limit-burst 10 
用来比对一次同时涌入的封包是否超过10个，超过此上限的包将直接丢弃。
3）同时指定速率限制和触发阀值。
#iptables -A INPUT -p icmp -m limit –-limit 3/m –limit-burst 3
表示每分钟允许的最大包数量为限制速率（本例为3）加上当前的触发阀值burst数。任何情况下，都可保证3个数据包通过，触发阀值burst相当于允许额外的包数量。 
4）基于状态的匹配扩展（连接跟踪）
每个网络连接包括以下信息：源地址、目标地址、源端口、目的端口，称为套接字对（socket pairs）；协议类型、连接状态（TCP协议）
和超时时间等。防火墙把这些信息称为状态（stateful）。状态包过滤防火墙能在内存中维护一个跟踪状态的表，比简单包过滤防火墙具有更大的安全性，命令格式如下： 
iptables -m state –-state [!]state [,state,state,state]
其中，state表是一个逗号分割的列表，用来指定连接状态，4种：
>NEW: 该包想要开始一个新的连接（重新连接或连接重定向）
>RELATED:该包是属于某个已经建立的连接所建立的新连接。举例：
FTP的数据传输连接和控制连接之间就是RELATED关系。
>ESTABLISHED：该包属于某个已经建立的连接。
>INVALID:该包不匹配于任何连接，通常这些包被DROP。
例如：
（1）在INPUT链添加一条规则，匹配已经建立的连接或由已经建立的连接所建立的新连接。即匹配所有的TCP回应包。
#iptables -A INPUT -m state –state RELATED,ESTABLISHED
（2）在INPUT链链添加一条规则，匹配所有从非eth0接口来的连接请求包。
#iptables -A INPUT -m state -–state NEW -i !eth0
又如，对于ftp连接可以使用下面的连接跟踪：
（1）被动（Passive）ftp连接模式。
#iptables -A INPUT -p tcp --sport 1024: --dport 1024: -m state –-state ESTABLISHED -j ACCEPT
#iptables -A OUTPUT -p tcp --sport 1024: --dport 1024: -m 
state -–state ESTABLISHED,RELATED -j ACCEPT
（2）主动（Active）ftp连接模式
#iptables -A INNPUT -p tcp --sport 20 -m state –-state ESTABLISHED,RELATED -j ACCEPT
#iptables -A OUTPUT -p tcp –OUTPUT -p tcp –dport 20 -m state --state ESTABLISHED -j ACCEPT
```













# SELinux 安全子系统



``` 
关闭selinux
[root@ipsan-node01 ~]# setenforce 0
setenforce: SELinux is disabled
[root@ipsan-node01 ~]# getenforce
Disabled
[root@ipsan-node01 ~]# cat /etc/sysconfig/selinux
.......
SELINUX=disabled
```









### [Centos7开放及查看端口](https://www.cnblogs.com/heqiuyong/p/10460150.html)

1、开放端口

**firewall-cmd --zone=public --add-port=5672/tcp --permanent**  # 开放5672端口

**firewall-cmd --zone=public --remove-port=5672/tcp --permanent** #关闭5672端口

**firewall-cmd --reload**  # 配置立即生效

 

2、查看防火墙所有开放的端口

**firewall-cmd --zone=public --list-ports**

 

3.、关闭防火墙

如果要开放的端口太多，嫌麻烦，可以关闭防火墙，安全性自行评估

**systemctl stop firewalld.service**

 

4、查看防火墙状态

 **firewall-cmd --state**

 

5、查看监听的端口

**netstat -lnpt**

![img](https://img2018.cnblogs.com/blog/1336432/201903/1336432-20190302110949754-1765820036.png)

*PS:centos7默认没有 netstat 命令，需要安装 net-tools 工具，yum install -y net-tools*

 

 

6、检查端口被哪个进程占用

**netstat -lnpt |grep 5672**

![img](https://img2018.cnblogs.com/blog/1336432/201903/1336432-20190302104128381-1210567174.png)

 

7、查看进程的详细信息

**ps 6832**

![img](https://img2018.cnblogs.com/blog/1336432/201903/1336432-20190302104342651-779103690.png)

 

8、中止进程

**kill -9 6832**