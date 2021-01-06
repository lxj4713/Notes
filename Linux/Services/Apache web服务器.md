

[toc]



### 安装Http

``` bash
yum install httpd  # 安装apache

systemctl start httpd  # 启动
systemctl enable httpd  # 加入开机启动
```



### 配置文件

| 服务目录     | /etc/httpd                 |
| ------------ | -------------------------- |
| 主配置文件   | /etc/httpd/conf/httpd.conf |
| 网站数据目录 | /var/www/html              |
| 访问日志     | /var/log/httpd/access_log  |
| 错误日志     | /var/log/httpd/error_log   |



`vim /etc/httpd/conf/httpd.conf`

配置httpd服务程序时最常用的参数以及用途描述

| ServerRoot     | 服务目录                  |
| -------------- | ------------------------- |
| ServerAdmin    | 管理员邮箱                |
| User           | 运行服务的用户            |
| Group          | 运行服务的用户组          |
| ServerName     | 网站服务器的域名          |
| DocumentRoot   | 网站数据目录              |
| Listen         | 监听的IP地址与端口号      |
| DirectoryIndex | 默认的索引页页面          |
| ErrorLog       | 错误日志文件              |
| CustomLog      | 访问日志文件              |
| Timeout        | 网页超时时间，默认为300秒 |

DocumentRoot 查看网站数据目录,修改index页面

``` bash
echo "Welcome To LinuxProbe.Com" > /var/www/html/index.html
```





#### 个人用户主页功能

修改配置` vim /etc/httpd/conf.d/userdir.conf`

注释17行 UserDir disabled功能,打开24行 UserDir public_html

UserDir参数表示网站数据在用户家目录中的保存目录名称，即public_html目录

``` shell
 11 <IfModule mod_userdir.c>
 12     #
 13     # UserDir is disabled by default since it can confirm the presence
 14     # of a username on the system (depending on home directory
 15     # permissions).
 16     #
 17     # UserDir disabled
 18
 19     #
 20     # To enable requests to /~user/ to serve the user's public_html
 21     # directory, remove the "UserDir disabled" line above, and uncomment
 22     # the following line instead:
 23     #
 24     UserDir public_html
 25 </IfModule>

```

修改个人用户主页

``` bash
➜  [/home/lxj] su - lxj
➜  [/home/lxj] mkdir public_html
➜  [/home/lxj] echo "This is lxj websit" > public_html/index.html
➜  [/home/lxj] chmod 755 /home/lxj
```

网页浏览个人主页: http://192.168.44.128/~lxj/



getsebool命令查询并过滤出所有与HTTP协议相关的安全策略

用setsebool命令来修改SELinux策略中各条规则的布尔值,在setsebool命令后面加上-P参数，让修改后的SELinux策略规则永久生效且立即生效

``` bash
setsebool -P httpd_enable_homedirs=on
```





- [ ] TODO

    https://www.linuxprobe.com/chapter-10.html#105









``` bash
rpm -qa httpd       #查看是否安装有httpd
yum install httpd* -y     #安装httpd
systemctl start httpd.service 
systemctl status httpd.service
systemctl stop httpd.service
```



配置

| 服务目录     | /etc/httpd                 |
| ------------ | -------------------------- |
| 主配置文件   | /etc/httpd/conf/httpd.conf |
| 网站数据目录 | /var/www/html              |
| 访问日志     | /var/log/httpd/access_log  |
| 错误日志     | /var/log/httpd/error_log   |

```
		配置参数
			ServerRoot	服务目录
			ServerAdmin	管理员邮箱
			User	运行服务的用户
			Group	运行服务的用户组
			ServerName	网站服务器的域名
			DocumentRoot	网站数据目录
			Listen	监听的IP地址与端口号
			DirectoryIndex	默认的索引页页面
			ErrorLog	错误日志文件
			CustomLog	访问日志文件
			Timeout	网页超时时间,默认为300秒.
			Include	需要加载的其他文件

 httpd -t   #修改完后，使用httpd -t检查语法是否有误
```

