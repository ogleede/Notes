# homebrew 安装 java相关环境

## homebrew

https://zhuanlan.zhihu.com/p/90508170

镜像源：https://brew.idayer.com/guide/change-source/

最好选腾讯的，有钱真是快

cask命令改了，要用brew install --cask XXXX

## jdk

我下的是zulu8，听说这个编译更快一点

brew search zulu8

brew install homebrew/cask-versions/zulu8    这里记不太清了，安装完了之后再search找不到原来的路径了

自动配置环境变量

## typora

brew install typora

编辑：取消自动语法纠正，取消开头自动大写

## mysql

brew install mysql@5.7

https://www.cnblogs.com/haojile/p/13193861.html

brew list mysql@5.7  找到路径可以前台启动

.. ERROR! The server quit without updating PID file (/opt/homebrew/var/mysql/bogon.pid).

主要是没删干净，把var文件夹下面的也全删除，自己找去删

find / -name my.cnf

/opt/homebrew/etc/my.cnf
/opt/homebrew/Cellar/mysql@5.7/5.7.35/.bottle/etc/my.cnf

 mysql.server start

mysql -uroot -p

quit

用brew services start mysql就用brewservices stop mysql关闭

用mysql.server start 启动就用 mysql.server stop关闭

登录问题：

https://blog.csdn.net/vv19910825/article/details/82979563

查看数据位置：

show global variables like "%datadir%";

/opt/homebrew/var/mysql/

## maven

brew install  maven

brew list maven

cd /opt/homebrew/Cellar/maven/3.8.1/libexec/conf/

vim setting.xml

https://www.cnblogs.com/dalianpai/p/11850539.html

https://www.cnblogs.com/li1111xin/p/5844408.html

https://www.bilibili.com/video/BV12J411M7Sj?p=5

windows解压bin，配置两个环境变量，mac下好像自动配了

M2_HOME：maven目录下的bin目录

MAVEN_HOME:maven的目录

全局配置: ${M2_HOME}/conf/settings.xml 
用户配置: user.home/.m2/settings.xml 

用户配置高于全局配置

https://blog.csdn.net/weixin_42195292/article/details/104271831

* 项目架构管理工具
    * 现在就是导包的
* 核心思想：约定大于配置，有约束不要去违反
* 本地仓库：存放项目jar包，做缓存库，开发时会首先从本地仓库获取jar包，无法获取再去远程仓库（或者中央仓库）中下载jar包，并缓存到本地仓库以备将来使用
* /opt/homebrew/var/maven-3.8.1-repo
* 远程仓库：

mirrorof  :支持一些镜像操作

### idea 中maven使用

maven-web-app	

一定要配置环境变量

open ~/.bash_profile

添加

export M2_HOME=/usr/local/Cellar/maven/3.8.1/libexec
export PATH=$PATH:$M2_HOME/bin





## VSCODE

https://blog.csdn.net/weixin_30463341/article/details/97295622

code filename

