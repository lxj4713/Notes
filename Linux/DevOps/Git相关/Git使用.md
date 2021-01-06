<!-- toc -->
[toc]
<!-- toc -->





# Git基础

**安装**

linux安装git

``` bash
yum install -y git
```

源码安装

``` bash
wget 
```

**配置**

检查配置信息,及查看配置所在文件夹

``` bash
git config --list
git config --list --show-origin
```

配置用户信息

``` bash
#设置本地仓库的用户名和邮箱，用于关联远端
git config --golbal user.name "lxj4713"
git config --golbal user.email "lxianjin@foxmail.com"
```



**创建版本仓库**

``` bash
git init  # 初始化本地仓库
```



**暂存文件**

``` bash
git <file>
git add .
git add *
```



**查看当前文件状态**

``` bash
git status 
git status -s   # 简化输出
```



**查看修改**

``` bash
git diff   # 查看尚未暂存的文件更新了哪些部分

git diff --staged  #对比已暂存文件和最后一次提交的差异
git diff --cached  # 查看已暂存起来的变化

```



**忽略文件**

`.gitignore`



**查看提交历史**

``` bash
git log
git log --stat   #简略统计
```

详细输出参考：[git log](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2)





**提交**

```bash
git commit -m "提交说明"   
```



撤销操作

``` bash
git commit --amend   # 撤销最近一次的提交
git commit --amend -m "跟正信息说明"
```



取消暂存的文件

``` bash
git reset HEAD <file> ...
```



撤销对文件的修改

> `git checkout` 会使文件在本地所有的修改都消失

``` bash
git checkout -- <file>
```







##  远程仓库

查看远程仓库

``` bash
git remote [-v]  #显示简写和对应的URL
```



添加远程仓库

> `git clone`自行添加

添加一个远程Git仓库，同时指定一个方便的简写

``` bash
git reomote add <shortname> <url>  
```



从远程仓库中抓取与拉取

从远程拉取所有还没有的数据，也会有拥有所有分支的引用，可以查看或合并

``` bash
git fetch <remote>
```

`git fetch`不会自动合并或修改当前的工作，需要手动合并到当前工作

`git pull`  抓取后合并，TODO



推送到远程仓库

``` bash
git push <remote> <branch>

git push origin master
```



查看某个远程仓库

``` bash
git remote show origin
```



远程仓库的重命名和移除

``` bash
git remote rename lxj new_lxj  # 重命名

git remote remove lxj   # 移除远程仓库
```





标签

列出标签 `git tag ` `-l 或 --list`

``` bash
git tag
git tag -l "v1.1.0"
```

更多标签操作：[git文档-打标签](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%89%93%E6%A0%87%E7%AD%BE)





# Git 分支

创建分支

``` bash
git branch lxj 
```



切换分支

``` bash
git checkout lxj

git checkout -b lxj  #创建并切换到新分支
```





合并分支

``` bash
git merge lxj 
```



合并分支遇到冲突时

```html
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
 please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```

这表示 `HEAD` 所指示的版本（也就是你的 `master` 分支所在的位置，因为你在运行 merge 命令的时候已经检出到了这个分支）在这个区段的上半部分（`=======` 的上半部分），而 `iss53` 分支所指示的版本在 `=======` 的下半部分。 为了解决冲突，你必须选择使用由 `=======` 分割的两部分中的一个，或者你也可以自行合并这些内容



解决完所有文件里的冲突后，`git add`重新提交



分支合并之变基：[git文档：变基](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA)





分支管理

``` bash
git branch lxj -v #查看分支最后一次提交
```



查看哪些分支已经合并到当前分支

``` bash
$ git branch --merged 
  lxj
* master
```

如上，lxj分支已经合并到master当前分支了，分支名字前没有*号的分支，可以删除掉 如：`git branch -d`

查看包含未合并工作的分支

``` bash
git branch --no-merged
```



分支开发工作流

master 完全稳定的分支

devlop或者next平行分支，用来做后续开发或者测试稳定性

短期分支



### 远程分支

推送

推送到远程分支lxj

``` bash
git push origin lxj
```



推送本地的lxj分支，将其作为远程仓库的remote_lxj分支

```bash
git push origin lxj:romote_lxj
```



拉取

``` bash
git pull
git fetch
git merge
```





删除远程分支

``` bash
git push origin --delete remote_lxj
```





SSH 公钥

.pub 公钥

另外就是私钥

``` bash
ssh-keygen

id_dsa
id_ras
```



Git服务器

部署自己的[Git服务器(参考Git文档：服务器上的Git)](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E5%8D%8F%E8%AE%AE)

或者使用[GitLab(参考Git文档)](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-GitLab)



- [ ] TODO

    查看git相关工作流程[Git文档第5章](https://git-scm.com/book/zh/v2/%E5%88%86%E5%B8%83%E5%BC%8F-Git-%E5%88%86%E5%B8%83%E5%BC%8F%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B)





## GitHub

详细问题见:[GitHub 文档](https://docs.github.com/cn/free-pro-team@latest/github)

测试SSH连接

```shell
ssh -T git@github.com
```