# Linux
## 安装完 centos mini 后需要做什么
### 一、配置网络

1.将IP配置成静态

==vim /etc/sysconfig/network-scripts/ifcfg-eth0==

```
		ONBOOT=yes   //打开网桥
		BOOTPROTO=static  //将IP设置为静态分配的，默认的是：dhcp
		IPADDR="192.168.214.101" //指定IP地址
		NETMASK="255.255.255.0" //此项写不写都可以，掩码
		GATEWAY="192.168.214.2" // 网关
		
		#配置完成之后，重启网络服务
		centos6.x：service network restart
		centos7.x：systemctl network restart
```

#### 2.将IP和我们的计算机名进行绑定
==vim /etc/hosts==

```
		#添加如下内容注意：IP和计算机名不一定和我的一样
		192.168.214.101 hadoop101
	
		验证是否成功
		#通过ssh协议进行远程连接，登录的用户名和主机IP(主机名)
		ssh root@hadoop101
		
		#当我们执行上命令之后出现了对应的提示，就表示配置成功
		Are you sure you want to continue connecting (yes/no)? yes
```
### 二、安装软件
1.

```
#yum install epel-release
#yum install yum-axelget
#yum update//连同软件、系统内核一起更新升级 yum upgrade 只升级所有包，不升级软件和系统内核。
#yum -y install net-tools
#yum install gcc
#yum install gcc-c++
#yum install make
#yum install gdb
#yum install cmake
#yum install git
#yum install vim
#yum install clang
#yum install clang-analyzer
#yum install ntpdate -y
#vim /etc/sysconfig/clock //修改时间的配置文件，正确如下
	ZONE="Asia/Shanghai" //正确的配置，如果不是修改一下​
 
#yum install ntp -y //安装更新工具ntp​
#yum install setuptool -y //安装更新工具​
#ntpdate cn.pool.ntp.org //更新系统时间​
#hwclock --systohc//更新硬件时间​
#hwclock//查看硬件时间​
```
#### 2.查看Linux内核版本命令
查看Linux==内核版==本命令（两种方法）：
```
#cat /proc/version
#uname -a
```
查看Linux==系统版本==的命令（3种方法）：
```
#lsb_release -a//这个命令适用于所有的Linux发行版，包括Redhat、SuSE、Debian…等发行版。
#cat /etc/redhat-release //这种方法只适合Redhat系的Linux：
#cat /etc/issue //此命令也适用于所有的Linux发行版。
```
#### 3.安装中文帮助
解压:tar -zxvf manpages-zh-1.5.1.tar.gz
进入解压之后的目录 cd manpages-zh-1.5.1
配置安装参数:==./configure --disable-zhtw==
编译并安装: ==make&make install== //注意此处可能会报错,需要安装gcc编译器

#### 4.安装ZSH
oh my zsh是zsh shell的一个框架,所以先得装zsh:sudo yum -y install zsh
查看下版本是否安装成功: zsh --version
#chsh命令用来更换登录系统时使用的shell
查看当前系统的shell 列表:chsh -l
修改 shell:chsh -s /bin/zsh
查看当前的 Shell:echo $SHELL

#### 1)安装高版本的Git
oh my zsh 的自动安装脚本需要使用Git的环境,因此需要先把Git安装一下:必须是1.7.2或者更高版本
##### 安装编译所需依赖库
```
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel
yum install gcc perl-ExtUtils-MakeMaker
yum -y install gcc gcc-c++ libstdc++-devel
yum install -y tcl build-essential tk
```
##### 卸载Centos自带的git1.7.1
```
卸载Centos自带的git1.7.1 :yum remove git
下载新版本git并编译安装(网速原因,已经提前下载好了):略
解压:tar zxvf git-2.9.0.tar.gz
```
##### 安装
```
cd git-2.9.0//在编译过程中有可能会出现错误,安装对应的依赖即可
make prefix=/usr/local/git all
make prefix=/usr/local/git install
echo "export PATH=$PATH:/usr/local/git/bin" >> /etc/bashrc
source /etc/bashrc

检查版本:git --version
```
#### 2)按装 oh my zsh
```
sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
这只是一个Oh-my-zsh提供的一个自动安装脚本,只要安装好Git环境,zsh可以实现自动安装

配置主题
oh-my-zsh有一个核心配置文件,存放目录 ~/.zshrc
主题所在目录ls /用户/.oh-my-zsh/themes
ZSH_THEME = "random"//主题设置为随机主题
```
#### 3)配置插件
```
下载安装插件

语法高亮插件zsh-syntax-highlighting,执行下面指令自动安装
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-sy ntax-highlighting\

启用插件

vim ~/.zshrc  	//编辑~/.zshrc
vim ~/.zshrc 	//在plugins 后括号里添加安装的插件名字
plugins=( git zsh-syntax-highlighting)
source ~/.zshrc	//最后刷新
```
### 三、学习Shell 脚本

使用指定脚本执行器( 解释器)去执行一个命令集合(脚本)
学习的要求:能够编写简单的,同时能够看懂复杂的,因为这是大神的世界
#### 1.编写脚本的步骤
```
1.
在脚本的第一行我们需要声明脚本执行器:#!/bin/bash
2. shell脚本中的注释也是# ,第一行除外
3. 执行脚本
```
```
#查看当前系统的脚本执行器列表
chsh -l

#查看当前使用的脚本解释器
echo $SHELL
```


参数变量
$n $0 获取当前的脚本名称 $1-9 获取对应位置上的参数值
$# 表示参数总个数
$* 获取所有的参数,将参数当做一个整体进行处理
$@获取所有的参数,将参数分别进行处理
预定义变量
$? 表示最后一条命令是否执行成功 成功0 失败非0
运算符
我们使用的任何运算符要求该运算符两边必须要有空格,具体的使用方式
echo $((1 + 1)) echo $[1 + 1] expr 1 + 1
#### 2.流程控制语句

if
```
if [ 条件判断 ];then
需要执行的代码
fi

if [ 条件判断 ];then
代码
else if [ 条件判断 ];then
代码
fi
```
case
```
case 变量 in
1)
;;
*)
;;
esac
```

for
```
for(())
done
for xxx in
done
```
while
```
while [ 条件表达式 ]
done
```
