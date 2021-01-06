<!-- toc -->
[toc]
<!-- toc -->

# 手动安装

## 下载安装

**下载centos依赖包**

``` bash
yum install gcc wget gdb -y
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
yum install libffi-devel -y  
```



**下载并安装python**

``` bash
# 下载需要的python版本，并解压
wget [https://www.python.org/ftp/python/3.6.3/Python-3.6.3.tgz](http://www.python.org/ftp/python/3.6.2/Python-3.6.2.tgz)  
tar -xzvf Python-3.6.3.tar.gz
cd Python-3.6.3

# 创建并指定安装路径
mkdir -p /opt/python3.6
./configure --prefix=/opt/python3.6 
#(./configure --enable-shared --prefix=/opt/python3.6 加上--enable-shared参数就可以生成对应动态链接库 pyinstaller 模块要用) 
make && make install 

# 创建系统命令软链接python和pip
ln -s /opt/python3.6/bin/python3.6 /usr/bin/python3
ln -s /opt/python3.6/bin/pip3.6 /usr/bin/pip
```



## 虚拟环境

**[虚拟环境virtualenv及管理工具virtualenvwrapper](https://pythonguidecn.readthedocs.io/zh/latest/dev/virtualenvs.html)**

``` bash
pip install --upgrade pip  # 更新pip
pip install virtualenv     # 安装虚拟环境
find / -name virtualenv # 查找virtualenv安装路径（/opt/python3.6/bin/virtualenv）
ln -s /opt/python3.6/bin/virtualenv /usr/bin/virtualenv  #创建virtualenv软链接
virtualenv -p /opt/python3.6/bin/python3.6 venv  #创建venv虚拟环境，-p参数指定Python解释器程序路径，可不用
source /centos/lxj/virtualenv/venv/bin/activate  #激活虚拟环境
deactivate    # 退出虚拟环境

#编辑虚拟环境快速启动别名
vi ~/.bashrc   
alias venv='source /home/lxj/env/venv/bin/activate && cd /home/lxj/'
source ~/.bashrc  # 执行生效
```

**[virtualenvwrapper 虚拟环境管理工具]( http://pythonguidecn.readthedocs.io/zh/latest/dev/virtualenvs.html)**

``` bash
virtualenv --relocatable 创建虚拟沙盒，代替环境安装 打包虚拟环境
[lxj@centos ~]$ /opt/python3.6/bin/virtualenv --relocatable venv/
移植到新的主机上后修改环境变量 VIRTUAL_ENV
**virtualenvwrapper** 虚拟环境集中管理
yum install python-setuptools python-devel
pip install virtualenvwrapper  # 安装 pip install virtualenvwrapper-win Windows安装
配置环境变量，在~/.bashrc写入以下内容
export WORKON_HOME=~/Envs                # virtualenvwrapper存放虚拟环境目录
source /usr/local/bin/virtualenvwrapper.sh　     # 会安装到python的bin目录下，路径是python安装目录下bin/virtualenvwrapper.sh
source ~/.bashrc　#读入配置文件，立即生效
基本命令
**mkvirtualenv** **--python**=/usr/local/python3.5.3/bin/python venv  #创建虚拟环境，指定python版本解释器
**workon**    # 查看当前的虚拟环境目录
**workon venv** # 切换到虚拟环境
deactivate  # 退出虚拟环境
rmvirtualenv venv  # 删除虚拟环境
(venv) [lxj@centos ~]$ pip install -i [https://pypi.douban.com/simple/](http://pypi.douban.com/simple/) --trusted-host=[pypi.douban.com/simple/](http://pypi.douban.com/simple/) -r requirements.txt
mkdir -p /opt/python36/
chmod -R a+x /opt/python36/
chown -R lxj /opt/python36
```







## **pip使用**

**导出批量安装**

``` bash
pip freeze > requirements.txt      # 将已安装的模块导出
pip install -r requirements.txt    # 批量安装
```



**使用源安装**

``` bash
pip install -i http://pypi.douban.com/simple/ --trusted-host=pypi.douban.com/simple django
```



**永久修改pip源** [pip使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/pypi/)

写在配置文件里 vi ~/.pip/pip.conf (没有就创建)

``` bash
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
# [install]
# trusted-host = pypi.douban.com     # 用https需要添加这行
```

**pip国内源**

> 豆瓣(douban) http://pypi.douban.com/simple/
>
> 阿里云 [https://](https://mirrors.aliyun.com/pypi/simple/)[mirrors.aliyun.com/pypi/simple/](https://mirrors.aliyun.com/pypi/simple/)
>
> 清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
>
> 中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/





**setup.py 安装模块**

``` bash
# 安装setuptools
# 下载setuptools源码包: https://pypi.python.org/pypi/setuptools
setup_url = 'https://files.pythonhosted.org/packages/68/75/d1d7b7340b9eb6e0388bf95729e63c410b381eb71fe8875cdfd949d8f9ce/setuptools-45.2.0.zip'
# 解压后进入目录
python setup.py install
# 安装pip
# pip源码包: https://pypi.python.org/pypi/pip#downloads
python setup.py build (可以不用build，直接install)
python setup.py install
```





# 自动安装脚本

``` python
# -*- coding: utf-8 -*-
# centos下用python2自动安装开发环境脚本
import os

class RunTool(object):
    '''安装工具/包/网址'''
    def __init__(self):
        pass
    def run_cmd(self, cmd):
        result = os.system(cmd)
        if not result == 0:
            return False  # TODO 此处报错处理应用raise处理
        return True
    def download_packets(self, url):
        # TODO 需判断URL为正确链接格式
        # TODO 此处下载需要进行重试及下载完成验证
        download_filename = url.split('/')[-1]
        if not self.check_exist_path(download_filename):
            download_cmd = 'wget %s' % (url)
            if self.run_cmd(download_cmd):
                if not self.check_exist_path(download_filename):
                    return False
                return download_filename
        return download_filename
    def check_exist_path(self, path):
        return os.path.exists(path)
    def switch_path(self, path):
        '''切换工作路径'''
        return os.chdir(path)
    def yum_install(self, packet):
        return self.run_cmd('yum install -y %s' % (packet))

class LinuxInstall(RunTool):
    '''Linux环境及扩展安装'''
    def __init__(self):
        pass
    def check_install(self, base_packets):
        '''检查linux包是否安装'''
        has_been_install = []
        for packet in base_packets:
            rpm_cmd = "rpm -qa  | egrep -i  '%s'" % (packet)
            rpm_results = os.popen(rpm_cmd).readlines()  # 得到的结果列表
            if rpm_results:
                for result in rpm_results:
                    if packet in result:
                        has_been_install.append(packet)
        has_install = list(set(has_been_install))
        print('Have been install %s' % (has_install))
        for install_packets in has_install:
            base_packets.remove(install_packets)
        return base_packets
    def replace_yum(self, yum_path, yum_url, old_yum):
        '''替换yum源'''
        # help doc: http://mirrors.163.com/.help/centos.html
        self.switch_path(yum_path)
        install_wget_name = self.check_install(['wget'])
        if len(install_wget_name) == 1:
            install_name = install_wget_name.pop()
            if self.yum_install(install_name):
                back_cmd = 'mv %s %s.back' % (old_yum, old_yum)
                if self.run_cmd(back_cmd):
                    yum_file_name = self.download_packets(yum_url)
                    if self.check_exist_path(yum_file_name):
                        self.run_cmd('yum clean all')
                        self.run_cmd('yum makecache')
    def install_linux_packets(self, base_packets):
        '''安装linux包'''
        install_packets = self.check_install(base_packets)
        if not install_packets:
            packets_str = ','.join(install_packets)
            print('%s packets has been install' % (packets_str))
            return True
        for packet in install_packets:
            if self.run_cmd('yum install -y %s' % (packet)):
                if self.check_install([packet]):
                    print('install %s packets failed' % (packet))
                    return False
                print('install %s packets success' % (packet))
        return True

class PythonInstall(RunTool):
    '''python3环境及虚拟环境安装'''
    def __init__(self, python_path):
        self.python_path = python_path
        self.virtualenv_install_path = self.python_path + '/bin/virtualenv'
    def install_python3(self, python3_down_url):
        # #TODO 除了下载链接，可以提供下载路径
        if not self.check_exist_path(self.python_path):
            self.run_cmd('mkdir -p %s' % (self.python_path))
        self.switch_path(self.python_path)
        down_filename = self.download_packets(python3_down_url)
        # 解压到指定路径
        tar_python_cmd = 'tar -xzvf %s -C /%s' % (
            down_filename, self.python_path)
        self.run_cmd(tar_python_cmd)
        tar_path = '.'.join(down_filename.split('.')[:-1])
        self.switch_path('%s/%s' % (self.python_path, tar_path))
        # 指定安装的python路径
        install_python3_cmd = './configure --prefix=%s' % (self.python_path)
        self.run_cmd(install_python3_cmd)
        self.run_cmd('make && make install')
        # TODO判断软连接文件有问题
        if self.check_exist_path('/usr/bin/python3'):
            self.run_cmd('rm -rf /usr/bin/python3')
        # 创建软链接
        self.run_cmd('ln -s %s/bin/python3  /usr/bin/python3' %
                     (self.python_path))
        # 测试安装是否成功
        if not self.run_cmd('python3 -V'):
            print('install python3 failed')
            return False
        print('install python3 success')
        return True
    def install_virtualenv(self, env_name, user):
        if self.run_cmd('%s/bin/pip3 install -i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com virtualenv' % (self.python_path)):
            self.run_cmd('rm -rf /usr/bin/virtualenv')
            virtualenv_ln_cmd = 'ln -s %s /usr/bin/virtualenv' % (
                self.virtualenv_install_path)
            if not self.run_cmd(virtualenv_ln_cmd):
                return 'install virtualenv failed'
        virtualenv_path = '/home/%s' % (user)
        if not self.check_exist_path(virtualenv_path):
            self.run_cmd('mkdir -p %s' % (virtualenv_path))
        self.switch_path(virtualenv_path)
        chown_cmd = 'chown -R %s:%s %s' % (user, user, self.python_path)
        self.run_cmd(chown_cmd)
        virtualenv_cmd = 'virtualenv -p %s/bin/python3 %s' % (
            self.python_path, env_name)
        if self.run_cmd(virtualenv_cmd):
            env_path = '%s/%s' % (virtualenv_path, env_name)
            chown_env_cmd = 'chown -R %s:%s %s' % (user, user, env_path)
            self.run_cmd(chown_env_cmd)
        # 激活虚拟环境
        virtualenv_test_cmd = 'source %s/%s/bin/activate' % (
            virtualenv_path, env_name)
        if self.run_cmd(virtualenv_test_cmd):
            print('install virtualenv success')
            return 'install virtualenv success'

class ConfigArgs(object):
    BASE_PACKETS = ['gcc', 'wget', 'gdb', 'vim','net-tools']
    PYTHON_RELY = ['zlib-devel', 'bzip2-devel', 'openssl-devel', 'ncurses-devel', 'sqlite-devel',
                   'readline-devel', 'tk-devel', 'gdbm-devel', 'db4-devel', 'libpcap-devel', 'xz-devel', 'libffi-devel']
    YUM_PATH = '/etc/yum.repos.d'
    YUM_URL = 'http://mirrors.163.com/.help/CentOS7-Base-163.repo'
    OLD_YUM = 'CentOS-Base.repo'
    PYTHON_URL = 'https://www.python.org/ftp/python/3.6.3/Python-3.6.3.tgz'
    PIP_URL = 'https://files.pythonhosted.org/packages/8e/76/66066b7bc71817238924c7e4b448abdb17eb0c92d645769c223f9ace478f/pip-20.0.2.tar.gz'
    PYTHON_PATH = '/tmp/python3'
    ENV_NAME = 'env'
    NORMAL_USER = 'cen'

def main():
    python_install = PythonInstall(ConfigArgs.PYTHON_PATH)
    linux_install = LinuxInstall()
    linux_install.replace_yum(
        ConfigArgs.YUM_PATH, ConfigArgs.YUM_URL, ConfigArgs.OLD_YUM)
    linux_base = linux_install.install_linux_packets(
        ConfigArgs.BASE_PACKETS+ConfigArgs.PYTHON_RELY)
    if linux_base:
        #     # import pdb
        #     # pdb.set_trace()
        result = python_install.install_python3(ConfigArgs.PYTHON_URL)
        if result:
            python_install.install_virtualenv(
                ConfigArgs.ENV_NAME, ConfigArgs.NORMAL_USER)
    result = python_install.install_python3(ConfigArgs.PYTHON_URL)
    if result:
        python_install.install_virtualenv(
            ConfigArgs.ENV_NAME, ConfigArgs.NORMAL_USER)

if __name__ == "__main__":
    main()

```

