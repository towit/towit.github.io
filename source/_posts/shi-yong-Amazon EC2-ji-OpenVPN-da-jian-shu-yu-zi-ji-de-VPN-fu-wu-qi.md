title: 使用Amazon EC2及OpenVPN搭建属于自己的VPN服务器 
copyright: true
date: 2017-10-30 11:03:47
tags: OpenVPN VPN服务器
categories: 服务器
---

## 本文基于`ubuntu或者debian`，其他操作系统请参考变更

sudo wget http://swupdate.openvpn.org/as/openvpn-as-2.5-Ubuntu16.amd_64.deb
注意： 如果下载不成功可以到以下地址下载，手动上传至服务器： 

https://pan.baidu.com/s/1kWiEDYf 
【密码：v7e9】
系统会下载Ubuntu环境下的OpenVPN AS 2.5 64位版本。接着我们输入

ubuntu:~$ sudo dpkg -i openvpn-as-2.5-Ubuntu16.amd_64.deb
进行安装。如果你看到以下信息，说明安装成功：

Selecting previously unselected package openvpn-as-2.1.4b-Ubuntu16.amd_64.deb
(Reading database ... 140897 files and directories currently installed.)
Preparing to unpack openvpn-as-2.1.4b-Ubuntu16.amd_64.deb 
Unpacking openvpn-as (2.1.4b-Ubuntu16) ...
Setting up openvpn-as (2.1.4b-Ubuntu16) ...
The Access Server has been successfully installed in /usr/local/openvpn_as
Configuration log file has been written to /usr/local/openvpn_as/init.log
Please enter "passwd openvpn" to set the initial
administrative password, then login as "openvpn" to continue
configuration here: https://your-ip-address:943/admin

Access Server web UIs are available here:
Admin  UI: https://your-ip-address:943/admin
Client UI: https://your-ip-address:943/
但是实际上我们还需要手动再重新配置一遍：

ubuntu:~$ sudo /usr/local/openvpn_as/bin/ovpn-init --force
一开始显示的条款我们需要手动输入yes，其他的选项除了以下这项，其他全部直接点击回车，保留默认值：

Please specify the network interface and IP address to be
used by the Admin Web UI:
(1) all interfaces: 0.0.0.0
(2) eth0: 192.168.1.112
Please enter the option number from the list above (1-3).
> Press Enter for default [2]:
注意，一定要输入1（阿拉伯数字1）并按回车确定以选择all interfaces: 0.0.0.0。这个步骤非常重要，否则你将不能连接上你的VPN服务器。

如果你全部按照我所说的步骤来做的话，那么完成后，我们就可以设置Admin UI的密码了：

ubuntu:~$ sudo passwd openvpn
该命令是linux系统下用于更改用户密码的。这里我们的用户名是openvpn，按照提示更改密码即可。输入完密码，我们还有一项重要工作，那就是设置服务器的Security Group来保证能通过TCP连接443和 943端口以及UDP连接1194端口。 
我们在Amazon服务器管理后台左侧面板中，选择Security Groups，然后点击右侧的Edit按钮，加入以下几条规则：

add rules

点击保存就可以了。


--------------------------------------------------------------------------------

好了，现在我们可以开始连接我们的Admin UI了。首先在Amazon后台查询一下你服务器的公共IP，然后在浏览器中输入：

https://server-public-ip-address:943/admin
注意，请用你的服务器的public ip替换上面的server-public-ip-address。回车后我们会看到显示ssl连接不安全的提示，忽略这个提示，点击继续，会弹出Admin UI的界面： 
openvpn as admin ui

在这里我们输入之前配置的用户名和密码（用户名为openvpn）即可进入后台管理界面： 
server network settings

这个时候我们点击左侧界面的Server Network Settings，然后在Hostname or IP Address一栏，填写上我们服务器的public ip。注意，这步非常非常非常重要（必须说三遍，否则连接不上服务器）。

注意保存一下刚才的设置。然后页面顶部会出现一个按钮，请点击更新服务器状态： 
Openvpn Update Running Server


--------------------------------------------------------------------------------

好了！现在我们就可以去下载客户端了：

Windows用户请点击以下链接下载客户端（Linux用户请往后看）：

openvpn-install-2.4.4-I601
上面这个是OpenVPN官方Windows安装包，打不开的话用迅雷下，不放心的请自己从官网下。下载好之后安装，然后打开会提示我们还没有导入配置文件。没关系，这个时候我们在浏览器输入

https://server-public-ip-address:943
请用自己的公共ip替换上面的server-public-ip-address。回车，然后我们会进入到Client UI中：

OpenVPN AS Client UI

右下角下拉菜单里，选择login，然后输入我们之前设置的用户名和密码（用户名openvpn），登录，会出现以下界面：

Client Profile

点击最底下的Yourself (user-locked profile)链接，会下载一个名为client.ovpn的文件，将其保存在桌面。在桌面找到这个文件，将这个文件放入刚才安装的客户端的文件夹中（注意，需要管理员权限）：

C:\Program Files\OpenVPN\config
好了，再切换到我们的客户端，发现弹出了输入用户名和密码的选项了：

OpenVPN Client

输入我们的用户名和密码，然后等待连接成功。现在我们就可以上网了！


--------------------------------------------------------------------------------

Linux用户（这里以Ubuntu 16.04 LTS为例）请输入命令：

$ sudo apt-get install openvpn
完成后按照Windows用户步骤中提到的，将client.ovpn下载至本地。

接着我们需要在client.ovpn中添加以下两行：

up /etc/openvpn/update-resolv-conf
down /etc/openvpn/update-resolv-conf
保存后我们就可以运行openvpn了：

$ sudo openvpn --script-security 2 --config client.ovpn
