<!-- toc -->
[toc]
<!-- toc -->

# zsh介绍

zsh 是一款功能强大的 shell 软件，它可以兼容 bash，并且提供了很多高效的改进。它是Linux里最庞大的一种shell，它有84个内部命令，也提供了更为强大的功能:

> 1.更好的自动补全  
>
> 2.更好的文件名展开  
>
> 3.丰富的插件  
>
> 4.强大的定制性

但是由于配置过于复杂，一般情况下，我们不会使用该shell，直到「[oh my zsh](https://ohmyz.sh/)」的出现。



# zsh 及 oh my zsh安装

``` bash
yum install zsh -y #zsh安装
chsh -s /bin/zsh   #切换系统shell为zsh
cat /etc/shells    #查看shell 


#curl或wget安装 oh my zsh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"  
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)" 
```



# 配置文件

``` bash
#切换shell为zsh后之前的环境变量就会失效
vim .zshrc     #修改zsh配置，可添加之前的环境变量
# User configuration
source ~/.bash_profile

source .zshrc  #修改后使配置生效
```



# oh my zsh主题配置及自定义修改

``` shell
ls ~/.oh-my-zsh/themes   #查看已有主题 

echo $ZSH_THEME     #查看当前主题

vim ~/.zshrc
ZSH_THEME="robbyrussell"      #修改配置文件.zshrc中主题配置项,也可设置为随机random
```

默认的 'robbyrussell'主题就已经很好，但缺少绝对目录路径和用户名的区分，可自定义主题文件robbyrussell.zsh-theme

为了避免oh-my-zsh的更新版本和自己修改的有冲突，最好不要修改原配置文件，可将其拷贝并重新命名为自己的主题文件myrobbyrussell.zsh-theme，并对其修改

``` bash
vi ~/.oh-my-zsh/themes/robbyrussell.zsh-theme   #修改 robbyrussell 主题的配置文件

# $USER 添加终端用户名及修改为绝对路径$PWD
PROMPT="%(?:%{$fg_bold[green]%}$USER ➜ :%{$fg_bold[red]%}$USER ➜ )"     
PROMPT+=' %{$fg[cyan]%}[$PWD]%{$reset_color%} $(git_prompt_info)'

```

目前采取做法是，配置不同的颜色来加以区分root及其他用户 ([更多主题方案](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes))



# oh my zsh 插件配置

``` bash
ls ~/.oh-my-zsh/plugins  #查看已存在的插件

zsh-autosuggestions  #根据历史输入指令的记录即时的提示
git clone https://github.com/zsh-users/zsh-autosuggestions.git       #插件下载

zsh-syntax-highlighting #命令高亮插件，正确的拼写会是绿色标识,否则是红色，路径带有下划线时表示可用路径
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git   #插件下载

plugins=(git zsh-autosuggestions zsh-syntax-highlighting)  #修改配置文件.zshrc中插件配置项
source ~/.zshrc  #更新配置，使插件生效
```



# 更新/卸载 oh my zsh

``` bash
vim ~/.zshrc
disable_update_prompt = true  #自动升级不提示，zsh默认会提示升级
disable_auto_update = true     #禁用自动升级
upgrade_oh_my_zsh         #手动更新
uninstall_oh_my_zsh zsh   #卸载oh-my-zsh
```





# 相关参考说明

``` shell
local ret_status="%(?:%{$fg_bold[green]%}➜ :%{$fg_bold[red]%}➜ )"
PROMPT='${ret_status} %{$fg[cyan]%}%~%{$reset_color%} $(git_prompt_info)'

ZSH_THEME_GIT_PROMPT_PREFIX="%{$fg_bold[blue]%}git:(%{$fg[red]%}"
ZSH_THEME_GIT_PROMPT_SUFFIX="%{$reset_color%} "
ZSH_THEME_GIT_PROMPT_DIRTY="%{$fg[blue]%}) %{$fg[yellow]%}✗"
ZSH_THEME_GIT_PROMPT_CLEAN="%{$fg[blue]%})"

#那个%{$fg[cyan]%}%c%{$reset_color%}中的%c就是罪魁祸首，是当前文件夹名的意思，我们把它修改为[$PWD]，整个也就变成了%{$fg[cyan]%}[$PWD]%{$reset_color%}  
#$PWD是终端自带变量，值为当前路径，我们做的只是把它取出来而已
```



``` shell
PROMPT='%{$fg[green]%}%m@%{$fg[magenta]%}%(?..%?%1v)%n:%{$reset_color%}%{$fg[cyan]%}%~#'
PROMPT='%{$fg_bold[red]%}-> %{$fg_bold[green]%}%p%{$fg[cyan]%}%d %{$fg_bold[blue]%}$(git_prompt_info)%{$fg_bold[blue]%}% %{$reset_color%}~#:'
PROMPT='%{$fg_bold[red]%}-> %{$fg_bold[green]%}%p%{$fg[cyan]%}%d %{$fg_bold[blue]%}$(git_prompt_info)%{$fg_bold[blue]%}% %{$fg[magenta]%}%(?..%?%1v)%{$reset_color%}~#: '
PROMPT='%{$fg_bold[red]%}-> %{$fg_bold[magenta]%}%n%{$fg_bold[cyan]%}@%{$fg[green]%}%m %{$fg_bold[green]%}%p%{$fg[cyan]%}%~ %{$fg_bold[blue]%}$(git_prompt_info)%{$fg_bold[blue]%}% %{$fg[magenta]%}%(?..%?%1v)%{$fg_bold[blue]%}? %{$fg[yellow]%}# '

```



| **code** | **info**                               |
| -------- | -------------------------------------- |
| %T       | 系统时间（时：分）                     |
| %*       | 系统时间（时：分：秒）                 |
| %D       | 系统日期（年-月-日）                   |
| %n       | 你的用户名                             |
| %B  - %b | 开始到结束使用粗体打印                 |
| %U  - %u | 开始到结束使用下划线打印               |
| %d       | 你目前的工作目录                       |
| %~       | 你目前的工作目录相对于～的相对路径     |
| %M       | 计算机的主机名                         |
| %m       | 计算机的主机名（在第一个句号之前截断） |
| %l       | 你当前的tty                            |
| %n       | 登录名                                 |