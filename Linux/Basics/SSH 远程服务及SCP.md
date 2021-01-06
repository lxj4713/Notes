<!-- toc -->
[toc]
<!-- toc -->



# SSH远程服务

> ssh 服务包括 ssh，scp，sftp，rsync。



## ssh安装

``` bash
yum list installed | grep openssh-server   # 验证是否安装
yum install openssh-server       # 安装

ps -e | grep sshd     [netstat -an | grep 22]      # 检查进程或端口号

systemctl enable sshd.service   # sshd 服务添加至自启动列表中
systemctl list-unit-files | grep sshd  #查看是否开启了sshd 服务自启动
```



## ssh 远程连接

``` bash
ssh [-p(小写)] root@192.168.137.128   #ssh远程连接

ssh root@172.25.0.11 touch/root/Desktop/file
ssh 远程主机用户@远程主机ip command ##直接在远程主机运行某条命令
```



## ssh配置文件

``` bash
vim /etc/ssh/sshd_config
```

| 配置文件参数                            | 作用                                                         |
| :-------------------------------------- | :----------------------------------------------------------- |
| Port 22                                 | TCP 默认端口为22，最好进行修改一个高位端口(TCP端口范围1-65535) |
| PubkeyAuthentication yes                | 密钥认证登陆 ssh-key                                         |
| AuthorizedKeysFile .ssh/authorized_keys | 默认公钥存放的位置                                           |
| PasswordAuthentication no               | 禁止密码登陆                                                 |
| PermitRootLogin no                      | 禁止root认证登录(生产环境最好禁止root和密码登陆)             |
| ListenAddress 192.168.137.130           | 限制ssh监听IP，有些服务器安全级别更高，不允许外网直接登陆，只有通过局域网才能登陆，可以在机房里设置一台能够被外网远程连接，其他的主机都通过这个机器进行远程连接即可 |
| StrictModes yes                         | 当远程用户的私钥改变时直接拒绝连接                           |
| MaxAuthTries 6                          | 最大密码尝试次数                                             |
| MaxSessions 10                          | 最大终端数                                                   |
| PermitEmptyPasswords no                 | 是否允许空密码登录(很不安全)                                 |

``` bash
systemctl restart sshd     #配置后需重启ssh服务
```





## ssh-key 安全密钥

``` bash
[root@localhost ~]# ssh-keygen

[root@localhost ~]# ssh-keygen[root@localhost ~]# ssh-keygen -t rsa -b 2048        -P '' 生成公私钥对。
-t 指定加密类型（rsa/dsa)
-b 指定密钥对加密长度（最好选1024位以上，如2048）

其中-P表示密码，-P '' 就表示空密码，也可以不用-P参数，这样就要三车回车，用-P就一次回车, -t表示密钥的加密类型,可以选择的加密类型有：dsa, ecdsa, ed25519, rsa, rsa1
[root@localhost .ssh]# ls /root/.ssh
id_rsa  id_rsa.pub
[root@localhost .ssh]# ssh-copy-id -i lxj@192.68.10.10    此条命令,上传公钥，上传到 服务器/etc/.ssh/目录下
[root@localhost .ssh]# scp /root/.ssh/id_rsa.pub root@192.168.10.10:/root/.ssh/authorized_keys   或者手动SCP拷贝到目标主机.

authorized_keys的权限要是600!!!
[root@localhost ~]# chmod 600 /root/.ssh/authorized_keys
[root@localhost .ssh]# ssh-copy-id @192.68.10.10

[root@localhost .ssh]# vim /etc/ssh/sshd_config    设置只允许密钥验证,然后重启服务器
78 PasswordAuthentication no
[root@localhost .ssh]# systemctl restart sshd


id_rsa 私钥 需要保密
id_rsa.pub 公钥,上传服务器
```



- [ ] TODO



## 访问控制

``` bash
#控制ssh客户端访问
vim /etc/hosts.deny


vim /etc/hosts.allow
sshd:172.25.254.250  ##允许250主机链接sshd
sshd:172.25.254.250， 172.25.254.180 ##允许250和180链接

sshd:ALL EXCEPT 172.25.254.200 ##只不允许200链接sshd


#ssh登陆提示修改该
vim /etc/motd ##显示登陆后字符
```





1. 密钥对验证：
   1. 连接前，将客户机client公钥放在服务器server上
   2. 连接时，client将用公钥去请求server，server会要求client接收server自己的公钥(server密钥对自动生成)
   3. 第一次连接时，server接收client发来的公钥，并验证本地存放的公钥和接收的client公钥是否一致，若不一致则直接拒接，若一致，则用client公钥加密一段信息，发给client
   4. client收到加密信息后，使用client私钥解密，并用server公钥加密信息，发给server
   5. server收到数据包后使用server私钥进行解密，若解密成功，内容和之前发送的信息一致，则建立了安全的远程连接


    ~~~
    centos6.x
    #临时关闭
    iptables -F    #清空防火墙规则
    setenforce 0   #临时关闭SELinux
    
    ~~~

    ~~~
    
    ~~~
    
    #永久关闭
    chkconfig iptables off       #设置防火墙开机不自启动
    sed -i '7s/enforcing/disabled/' /etc/selinux/config  #永久关闭SELinux
    # 需重启服务器才能生效
    ~~~

 


# SCP 远程传输

``` bash
scp（secure copy）是一个基于SSH协议在网络之间进行安全传输的命令
-v       显示详细的连接进度
-P(大写)       指定远程主机的sshd端口号
-r       用于传送文件夹/目录
-6       使用IPv6协议

scp [参数] 本地文件 远程帐户@远程IP地址:远程目录   上传到远程
[root@linuxprobe ~]# scp /root/readme.txt lxj@192.168.10.20:/home

scp [参数] 远程用户@远程IP地址:远程文件 本地目录   下载到本地主机
[root@linuxprobe ~]# scp root@192.168.10.20:/etc/redhat-release /root

SCP 不适合用来做备份,文件权限,日期属性等都会发生改变
```



 - [ ] TODO

# sftp



# rsync