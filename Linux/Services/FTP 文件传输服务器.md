<!-- toc -->
[toc]
<!-- toc -->



FTP 是文件传输协议,基于客户端/服务器模式，默认使用20、21号端口，其中端口20（数据端口）用于进行数据传输，端口21（命令端口）用于接受客户端发出的相关FTP命令与参数,vsftpd是最主流的FTP服务器程序.

空iptables防火墙的默认策略，并把当前已经被清理的防火墙策略状态保存下来

```bash
[root@linuxprobe ~]# iptables -F
[root@linuxprobe ~]# service iptables save
iptables: Saving firewall rules to /etc/sysconfig/iptables:[ OK ]
```





vsftpd服务程序的主配置文件（/etc/vsftpd/vsftpd.conf）

可以在grep命令后面添加-v参数，过滤并反选出没有包含井号（#）的参数行（即过滤掉所有的注释信息），然后将过滤后的参数行通过输出重定向符写回原始的主配置文件中

```bash
[root@linuxprobe ~]# mv /etc/vsftpd/vsftpd.conf /etc/vsftpd/vsftpd.conf_bak
[root@linuxprobe ~]# grep -v "#" /etc/vsftpd/vsftpd.conf_bak > /etc/vsftpd/vsftpd.conf
[root@linuxprobe ~]# cat /etc/vsftpd/vsftpd.conf
```



vsftpd服务程序常用的参数以及作用

| 参数                                              | 作用                                                         |
| ------------------------------------------------- | ------------------------------------------------------------ |
| listen=[YES\|NO]                                  | 是否以独立运行的方式监听服务                                 |
| listen_address=IP地址                             | 设置要监听的IP地址                                           |
| listen_port=21                                    | 设置FTP服务的监听端口                                        |
| download_enable＝[YES\|NO]                        | 是否允许下载文件                                             |
| userlist_enable=[YES\|NO] userlist_deny=[YES\|NO] | 设置用户列表为“允许”还是“禁止”操作                           |
| max_clients=0                                     | 最大客户端连接数，0为不限制                                  |
| max_per_ip=0                                      | 同一IP地址的最大连接数，0为不限制                            |
| anonymous_enable=[YES\|NO]                        | 是否允许匿名用户访问                                         |
| anon_upload_enable=[YES\|NO]                      | 是否允许匿名用户上传文件                                     |
| anon_umask=022                                    | 匿名用户上传文件的umask值                                    |
| anon_root=/var/ftp                                | 匿名用户的FTP根目录                                          |
| anon_mkdir_write_enable=[YES\|NO]                 | 是否允许匿名用户创建目录                                     |
| anon_other_write_enable=[YES\|NO]                 | 是否开放匿名用户的其他写入权限（包括重命名、删除等操作权限） |
| anon_max_rate=0                                   | 匿名用户的最大传输速率（字节/秒），0为不限制                 |
| local_enable=[YES\|NO]                            | 是否允许本地用户登录FTP                                      |
| local_umask=022                                   | 本地用户上传文件的umask值                                    |
| local_root=/var/ftp                               | 本地用户的FTP根目录                                          |
| chroot_local_user=[YES\|NO]                       | 是否将用户权限禁锢在FTP目录，以确保安全                      |
| local_max_rate=0                                  | 本地用户最大传输速率（字节/秒），0为不限制                   |





### 匿名开放模式

> 是一种最不安全的认证模式，任何人都可以无需密码验证而直接登录到FTP服务器
>
> 不建议在生产环境中放开以下这些权限

可以向匿名用户开放的权限参数以及作用

| 参数                        | 作用                               |
| --------------------------- | ---------------------------------- |
| anonymous_enable=YES        | 允许匿名访问模式                   |
| anon_umask=022              | 匿名用户上传文件的umask值          |
| anon_upload_enable=YES      | 允许匿名用户上传文件               |
| anon_mkdir_write_enable=YES | 允许匿名用户创建目录               |
| anon_other_write_enable=YES | 允许匿名用户修改目录名称或删除目录 |

``` bash
vim /etc/vsftpd/vsftpd.conf

[root@centos1 ~]# vim /etc/vsftpd/vsftpd.conf
anonymous_enable=YES
anon_umask=022
anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES
local_enable=YES
...
```





- [ ] TODO
- [ ] https://www.linuxprobe.com/chapter-11.html



### 本地用户模式

通过Linux系统本地的账户密码信息进行认证的模式，相较于匿名开放模式更安全，而且配置起来也很简单。但是如果被黑客破解了账户的信息，就可以畅通无阻地登录FTP服务器，从而完全控制整台服务器。



## 虚拟用户模式

> 这三种模式中最安全的一种认证模式，它需要为FTP服务单独建立用户数据库文件，虚拟出用来进行口令验证的账户信息，而这些账户信息在服务器系统中实际上是不存在的，仅供FTP服务程序进行认证使用。这样，即使黑客破解了账户信息也无法登录服务器，从而有效降低了破坏范围和影响。