---
title: 服务器搭建-centos
date: 2017-11-07 10:04:56
tags:
- 技术
- Design
category: 技术
lede: "基于centos配置服务环境"
thumbnail: 
---

## nginx
To set up the yum repository for RHEL/CentOS, create the file named /etc/yum.repos.d/nginx.repo with the following contents:

```
1. vi /etc/yum.repos.d/nginx.repo
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=0
enabled=1

2. yum install nginx 
3. service nginx start
```
Replace “OS” with “rhel” or “centos”, depending on the distribution used, and “OSRELEASE” with “6” or “7”, for 6.x or 7.x versions, respectively.  

## php


1. To install, first you must add the Webtatic EL yum repository information corresponding to your CentOS/RHEL version to yum:
```
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
```
2. Now you can install PHP 7.0’s mod_php SAPI (along with an opcode cache) by doing:
```
yum install php70w php70w-opcache
```

## MONGODB


 1. Configure the package management system (yum).
Create a /etc/yum.repos.d/mongodb-org-3.4.repo file so that you can install MongoDB directly, using yum.
```
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```
 2. Install the MongoDB packages and associated tools.
To install the latest stable version of MongoDB, issue the following command:
```
sudo yum install -y mongodb-org
```
 3. Start MongoDB.
You can start the mongod process by issuing the following command:
```
sudo service mongod start
```
 4. Verify that MongoDB has started successfully
You can verify that the mongod process has started successfully by checking the contents of the log file at /var/log/mongodb/mongod.log for a line reading
```
[initandlisten] waiting for connections on port <port>
```
where <port> is the port configured in /etc/mongod.conf, 27017 by default. 

 5. You can optionally ensure that MongoDB will start following a system reboot by issuing the following command:
```
sudo chkconfig mongod on
```
 6. Stop MongoDB.
As needed, you can stop the mongod process by issuing the following command:
```
sudo service mongod stop
```
 7. Restart MongoDB.
You can restart the mongod process by issuing the following command:
```
sudo service mongod restart
```
You can follow the state of the process for errors or important messages by watching the output in the /var/log/mongodb/mongod.log file. 

 8. Uninstall MongoDB Community Edition
To completely remove MongoDB from a system, you must remove the MongoDB applications themselves, the configuration files, and any directories containing data and logs. The following section guides you through the necessary steps.
**WARNING**
This process will completely remove MongoDB, its configuration, and all databases. This process is not reversible, so ensure that all of your configuration and data is backed up before proceeding.  

    **Stop MongoDB.**
Stop the mongod process by issuing the following command:
```
sudo service mongod stop
``` 
**Remove Packages.**
Remove any MongoDB packages that you had previously installed.
```sudo yum erase $(rpm -qa | grep mongodb-org)```
**Remove Data Directories.**
Remove MongoDB databases and log files. 

```
sudo rm -r /var/log/mongodb
sudo rm -r /var/lib/mongo```

**使用**
``` 
- 创建数据库超级管理员与数据库用户

打开命令提示符，输入mongod --auth（--auth 是以验证用户的方式启动）并执行 

- 再打开一个命令提示符窗口（请不要关闭上面的命令符窗口），输入 mongo并执行 

**以下为创建mongodb数据库说明：**

其中 admin 是你的超级用户，nodercms 是你的一个数据库， nodercms 是你的普通用户


- 然后输入use admin切换到 admin 库 输入以下代码创建**超级用户**

db.createUser({ user: 'admin', pwd: '超级用户密码', roles: [{ role: 'root', db: 'admin' }] })

- 输入`use admin`确保处于 admin 库

输入`db.auth('admin','超级用户密码')`来验证角色（以后每次登陆Mongo 都需要验证）

- 超级用户并不能直接使用，因为它是用来管理各个普通用户以及各个数据库的角色，因此还需要添加普通用户
输入`use nodercms`切换到最终要使用的数据库
输入以下代码来创建网站使用的数据库用户

db.createUser({ user: 'nodercms', pwd: '普通用户密码', roles: [{ role: 'readWrite', db: 'nodercms' }] })
```


