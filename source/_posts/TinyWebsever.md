---
title: TinyWebsever
date: 2024-08-06 23:01:59
tags: C++项目
categories: 项目
cover: /img/17.jpg
---

💕学习链接

[qinguoyi/TinyWebServer at raw_version (github.com)](https://github.com/qinguoyi/TinyWebServer/tree/raw_version)

[小白视角：一文读懂社长的TinyWebServer | HU (huixxi.github.io)](https://huixxi.github.io/2020/06/02/小白视角：一文读懂社长的TinyWebServer/#more)

[（一）TinyWebServer的环境配置与运行-CSDN博客](https://blog.csdn.net/weixin_46653651/article/details/133420059)

# 01 环境配置

## 创建VM虚拟机环境

## 安装数据库

### 🌷在终端输入以下内容

```c
# 安装mysql
sudo apt upgrade && sudo apt install mysql-server mysql-client libmysqlclient-dev
# 进入mysql
sudo mysql -u root
# 创建用户——这里根据自己所需配置
create user 'starry'@'%' identified by 'root';
# 给新用户符全部权限
grant all on *.* to 'starry'@'%';
 
# 退出mysql
exit
 
# 设置mysql远程连接
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
# 改成
bind-address = 0.0.0.0
# 重启mysql服务
sudo service mysql restart
```

### 🌷创建表

```c
# 进入mysql
sudo mysql -u root
# 创建数据库yourdb
create database yourdb;
# 使用数据库yourdb
use yourdb;
# 创建user表
CREATE TABLE user(
    username char(50) NULL,
    passwd char(50) NULL
)ENGINE=InnoDB;
 
# 添加数据
INSERT INTO user(username, passwd) VALUES('name', 'passwd');
```

```c
create user 'starry'@'%' identified by 'root';
```

>* `create user 'starry'@'%'`：这部分代码用于创建一个新的MySQL用户，用户名是`starry`。
>* `'%'`：表示这个用户可以从任何主机连接到MySQL服务器。这个通配符表示所有IP地址。
>* `identified by 'root'`：设置用户`starry`的密码为`root`。请注意，使用强密码而不是简单密码（如`root`）是更安全的做法。

```c
grant all on *.* to 'starry'@'%';
```

>`grant all`：授予所有权限，包括SELECT、INSERT、UPDATE、DELETE、CREATE、DROP等。
>
>`on *.*`：表示权限适用于所有数据库和所有表。第一个`*`表示所有数据库，第二个`*`表示所有表。
>
>`to 'starry'@'%'`：将这些权限赋予用户`starry`，该用户可以从任何主机连接。

这段代码的效果是：创建一个名为`starry`的新用户，密码为`root`，并且允许该用户从任何IP地址连接到MySQL服务器，并对所有数据库的所有表拥有所有权限。

参考链接：[VMware虚拟机共享主机v2rayN_vmware v2-CSDN博客](https://blog.csdn.net/csdner250/article/details/137168407)

🌼解释
网络地址转换

> 1）NAT 即 网络地址转换，可以将私有网络的 IP 地址映射到公有网络的 IP 地址上，以实现多个设备共享同一个公共 IP 地址来访问互联网。
>
> 2）而 VMware 共享本地代理这个过程，就用到了 NAT
>
> 3）首先，VMware-Ubuntu是一个虚拟机，在私有网络运行。主机通过 NAT 将虚拟机的 IP 地址映射到自己的公共 IP 上，再通过 V2rayN 访问其他网站
>
> 4）当 Ubuntu 通过虚拟网络发出请求时，它的请求会被 NAT 转换成主机的公共 IP，并通过主机的 V2rayN 转发到互联网上。
>
> 5）这样，Ubuntu 就能借助主机的代理访问其他网站，而外部网络只能看到主机的 IP，并不知道具体是哪个虚拟机在通信。

### 🌷检查Mysql状态

```c
systemctl status mysql.service
```

### 🌷查看表和表的内容

```c
sudo mysql -uroot -p //进入mysql环境	
show databases; //可以查看当前的数据库
show users;
select *from user;
```

### 🌷下载代码，编译运行

![img](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/256248a369bbba70daf7747f99b0d7ac.png)

#### 使用git克隆到本地

```c
# 没有git的使用下面命令(有git忽略此步骤)
sudo apt install git -y
# 执行下面的命令吧项目克隆到本地
git clone https://github.com/qinguoyi/TinyWebServer.git
```

**首先需要确认main.cpp里的数据库和你mysql数据库配置相同。**
**查看数据库名称和密码**

	cd /etc/mysql
	sudo vim debian.cnf

**进入项目，修改main.cpp文件配置，执行make**

```less
 cd TinyWebServer
 vi main.cpp
 make
```

编译Tinywebserver(编译运行)

```bash
cd Tinywebserver
sh ./build.sh
```

**运行可执行文件，访问项目**

```c
# 运行
./server  
# 打开浏览器，访问
http://127.0.0.1:9006/
```

打开VMware中的浏览器，输入网址后访问

显示登陆界面即可完成登录；

![image-20240807154534403](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240807154534403.png)

#### 压力测试

1.1安装依赖

```c
sudo apt-get install exuberant-ctags
```

1.2下载源码并安装

```c
wget http://blog.s135.com/soft/linux/webbench/webbench-1.5.tar.gz
tar zxvf webbench-1.5.tar.gz
cd webbench-1.5
make && sudo make install
```

> 参数
>
> ![image-20240807161420097](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240807161420097.png)

测试百度

```c
 webbench -c 500 -t 30 http://baidu.com/
```

![image-20240807161537559](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240807161537559.png)

测试TinyWebServer

打开新的终端，输入

```c
sudo su
cd TinyWebServer
./server
```

![image-20240807162021118](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240807162021118.png)

再开1个，webbench测压

```c
./webbench -c 1001 -t 5 http://127.0.0.1:9006/
```

