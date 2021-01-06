<!-- toc -->
[toc]
<!-- toc -->

# 定时任务-crontab

## crontab命令

| 参数 | 作用                       |
| ---- | -------------------------- |
| -e   | 编辑该用户的计时器设置     |
| -l   | 列出该用户的计时器设置     |
| -r   | 删除该用户的计时器设置     |
| -u   | 指定要设定计时器的用户名称 |

``` bash
crontab -l   #查看当前计划任务
crontab -e   #创建、编辑计划任务
crontab -r   #删除某条计划任务

crontab -e -u lxj  # 编辑lxj用户的定时任务
```

> minute   hour   day   month   week   command 
> 顺序：分 时 日 月 周 命令   
>
> minute分钟：0-59 之间的任何整数
> hour小时：1-23  
> day日期：1-31  
> mouth月份：1-12  
> week星期：0-6（0/7表示周日）  
> command：要执行的命令,系统命令或脚本文件  

> 可以用符号表示  
> \*   表示任何时刻  
> ,   指定列表范围，"1,3,5,7"  
> \-   指定整数范围，"1-5" 表示 "1,2,3,4,5" 1到5点  
> /n  表示执行频率，每个n的单位执行一次，如：*/1, 就表示每隔1个小时执行一次命令。也可以写成1-23/1

> 注意：  
> - 在crond服务的计划任务参数中，所有命令一定要用绝对路径的方式来写  
> - 计划任务中的“分”字段必须有数值，绝对不能为空或是*号，而“日”和“星期”字段不能同时使用，否则就会发生冲突。

| 定时任务设置举例            | 说明                                |
| :-------------------------- | :---------------------------------- |
| 43 21 * * *                 | 21:43 执行                          |
| 43 21 * * *                 | 21:43 执行                          |
| 15 05 * * *                 | 05:15 执行                          |
| 0 17 * * *                  | 17:00 执行                          |
| 0 17 * * 1                  | 每周一的17:00 执行                  |
| 0,10 17 * * 0,2,3           | 每周日,周二,周三的17:00和17:10 执行 |
| 0-10 17 1 * *               | 毎月1日从17:00到7:10毎隔1分钟 执行  |
| 0 0 1,15 * 1                | 毎月1日和15日和一日的0:00 执行      |
| 42 4 1 * *                  | 毎月1日的4:42分 执行                |
| 0 21 * * 1-6                | 周一到周六21:00 执行                |
| 0,10,20,30,40,50 * * * *    | 每隔10分 执行                       |
| */10 * * * *                | 每隔10分 执行                       |
| * 1 * * *                   | 从1:0到1:59每隔1分钟 执行           |
| 0 1 * * *                   | 1:00 执行                           |
| 0 */1 * * *                 | 毎时0分每隔1小时 执行               |
| 0 * * * *                   | 毎时0分 执行                        |
| 2 8-20/3 * * *              | 8:02,11:02,14:02,17:02,20:02 执行   |
| 30 5 1,15 * *               | 1日和15日的5:30 执行                |
| * * * * * rm -rf /mnt/*     | 每分钟删除一次                      |
| */2 * * * * rm -rf /mnt/*   | 每隔2分钟删除一次                   |
| * */2 * * * rm -rf /mnt/*   | 每隔2小时删除一次                   |
| * 10-12 * * * rm -rf /mnt/* | 每天十点到十二点每分钟删除一次      |
| * * 10,20 * * rm -rf /mnt/* | 每个月的十号二十号每分钟删除一次    |
| * * 10-20 * * rm -rf /mnt/* | 每个月的十号至二十号每分钟删除一次  |
| * * * 6 * rm -rf /mnt/*     | 六月份每分钟删除一次                |
| * * * * 2,5 rm -rf /mnt/*   | 每周二周五每分钟删除一次            |
| * * * * 2-5 rm -rf /mnt/*   | 每周二到周五每分钟删除一次          |

## crontab设置

~~~
service crond status           检查crond服务是否运行 /sbin/service  或 ps -elf|grep crond|grep -v "grep"   /sbin/service crond status      pgrep crond
service crond start            手动启动crontab服务  
service crond stop             关闭crontab服务
service crond restart          重启服务
service crond reload           重新载入配置
ntsysv                         查看crontab服务是否已设置为开机启动 (设置系统的各种服务)
chkconfig –level 35 crond on   加入开机自动启动  
安装cron
    yum install vixie-cron
    yun install crontabs


自动添加crontab 任务

1.编辑 /var/spool/cron/用户名文件
echo "* * * * * hostname >> /tmp/tmp.txt" >> /var/spool/cron/root

2.编辑 /etc/crontab 文件,   需指定用户名,文件定义为系统级定时任务 不建议添加非系统类定时任务，编辑该文件也需要root权限
echo "* * * * * root hostname >> /tmp/tmp.txt" >> /etc/crontab

3. 利用crontab -l 加 crontab file 两个命令实现自动添加，由于crontab file会覆盖原有定时任务，所以使用 crontab -l 先导出原有任务到临时文件 “conf” 再追加新定时任务
crontab -l > conf && echo "* * * * * hostname >> /tmp/tmp.txt" >> conf && crontab conf && rm -f conf


发起方式二#vim /etc/cron.d/filename[root@centos ~]# cat >> file << end分钟 小时 天 月 周 用户 动作* * * * * root touch /tmp/file1      #超级用户每分钟建立一个file1* * * * * student touch /tmp/file2   #普通用户每分钟建立一个file2

crontab 命令的执行权力设定

##系统所有用户默认不能执行crontab，只有在名单中出现的用户可以使用
/etc/cron.deny    #用户黑名单，在此名单中出现的用户不能执行crontab命令
/etc/cron.allow   #用户白名单，名单默认不存在，但名单一旦出现，黑名单失效

3.系统临时文件的管理


系统中服务在正常运行时会产生临时文件
systemd-tmpfiles –create /usr/lib/tmpfiles.d/*           #执行临时文件配置
/usr/lib/tmpfiles.d/filename.conf                        #系统中临时文件的配置
systemd-tmpfiles --clean /usr/lib/tmpfiles.d/*            #清里临时文件



2. 注意清理系统用户的邮件日志
每条任务调度执行完毕，系统都会将任务输出信息通过电子邮件的形式发送给当前系统用户，这样日积月累，日志信息会非常大，可能会影响系统的正常运行，因此，将每条任务进行重定向处理非常重要。
例如，可以在crontab文件中设置如下形式，忽略日志输出：
0 */3 * * * /usr/local/apache2/apachectl restart >/dev/null 2>&1
“/dev/null 2>&1”表示先将标准输出重定向到/dev/null，然后将标准错误重定向到标准输出，由于标准输出已经重定向到了/dev/null，因此标准错误也会重定向到/dev/null，这样日志输出问题就解决了。
3. 系统级任务调度与用户级任务调度
系统级任务调度主要完成系统的一些维护操作，用户级任务调度主要完成用户自定义的一些任务，可以将用户级任务调度放到系统级任务调度来完成（不建议这么做），但是反过来却不行，root用户的任务调度操作可以通过“crontab –uroot –e”来设置，也可以将调度任务直接写入/etc/crontab文件，需要注意的是，如果要定义一个定时重启系统的任务，就必须将任务放到/etc/crontab文件，即使在root用户下创建一个定时重启系统的任务也是无效的。
4. 其他注意事项
新创建的cron job，不会马上执行，至少要过2分钟才执行。如果重启cron则马上执行。
当crontab突然失效时，可以尝试/etc/init.d/crond restart解决问题。或者查看日志看某个job有没有执行/报错tail -f /var/log/cron。
千万别乱运行crontab -r。它从Crontab目录（/var/spool/cron）中删除用户的Crontab文件。删除了该用户的所有crontab都没了。
在crontab中%是有特殊含义的，表示换行的意思。如果要用的话必须进行转义\%，如经常用的date ‘+%Y%m%d’在crontab里是不会执行的，应该换成date ‘+\%Y\%m\%d’。




查看cron服务是否起作用：


查看定时任务是否准时调用了可以/var/log/cron中的运行信息

cat /var/log/cron
或
grep .*\.sh /var/log/cron   搜索.sh类型文件信息


crontab不执行，可进行如下操作：
crontab -u root /var/spool/cron/root
或
service crond restart  重启服务

~~~



# 二.一次性任务-st

at命令
一次性计划任务只执行一次，一般用于满足临时的工作需求。可以用at命令实现这种功能，只需要写成“at 时间”的形式就可以。

at -l           查看已设置好但未执行的一次性任务
atrm 任务序号    可将对应序号任务删除  使用at命令来设置一次性计划任务时，默认采用的是交互式方法

[root@linuxprobe ~]# at 23:30
at > systemctl restart httpd
at > 此处请同时按下Ctrl+d来结束编写计划任务
job 3 at Mon Apr 27 23:30:00 2015
[root@linuxprobe ~]# echo "systemctl restart httpd" | at 23:30  也可这种非交互式写法创建at任务
[root@linuxprobe ~]# at -l
3 Mon Apr 27 23:30:00 2016 a root
at now+1min  #延迟一分钟
at -f file   #延迟执行文件中的内容