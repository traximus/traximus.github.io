---
layout: post
title: "MAC下配置WebDAV服务器"
date: 2014-01-15 12:16:56 +0800
comments: true
categories: Mac,WebDAV,Server
---
---------------
**PS1：这是之前我做NAS项目时的WebDAV部分资料，既然弄了这个新的blog，就搬过来了。**<!--more-->

**PS2: 当时没有相关服务器，也找不到人来做，网上针对MAC系统搭建WebDAV服务器的资料也很少。源地址<http://gigaom2.wordpress.com/2010/10/01/how-to-enable-webdav-on-your-mac-for-iwork-on-ipad/> 原文针对iPad,我经过一定修改，在自己的lion上搭建成功。**

**PS3: 由于OS X 10.8以后好像没有“web共享“入口了(我自己是10.9没有该入口)所以先需要在终端中开启。**

---------------
#什么是WebDAV?
参见：http://baike.baidu.com/view/974023.htm

>webDAV也就是“基于网络的分布式创作和版本控制协议”，他是HTTP协议的扩展. webDAV被用于对web服务器进行读写访问，而且每一台mac都自带了一个web server;

>当你在系统设置中，打开“web共享”时，就已经运行了一个Apache web server; 虽然webDAV内置于mac系统中，但是在OS X中却没有默认开启；你需要使用管理员权限来开启webDAV配置。


---------------

#10.7.5以后没有“web共享”入口的解决办法
####1.开启apache
打开safari，输入`http://localhost`测试，如果显示 it works! 说明apache已经开启；否则，在终端中输入： **sudo apachectl restart**

####2.这时用户根目录`http://localhost/~username`还不能访问，为了让`~/Sites`下面的内容可以访问，继续以下的几个步骤
1. 查看username.conf文件是否存在
   
   ```
   ls /etc/apache2/users/
   ```
2.  若你的用户名.conf文件不存在，则需要手动建立一个
   
	```
	cd /etc/apache2/users/
	```
   
   	```
   	sodo vi username.conf
   	``` 
   	**注：username是你的mac用户名**
3. 粘贴以下内容到username.conf,注意把username替换为你自己的用户名

```
   　 <Directory "/Users/username/Sites/">

　　　　Options Indexes MultiViews

　　　　AllowOverride All

　　　　Order allow,deny

　　　　Allow from all　　

　　　　</Directory>
```
 保存，退出，重启apache: sudo apachectl restart

 **接下来，现在就可以开始正式配置WebDAV服务器了**


============



#开始搭建WebDAV服务器

###1.关闭web server(或者直接关闭apache server)
![turn off icon](/images/2014-01-16-01.jpeg)
###2.修改Apache Web Server配置文件
打开launchpad,打开终端**terminal**
注意，需要权限的时候，输入你的管理员密码，或者提前修改httpd.conf文件的读写权限；

* 转到etc文件夹：`cd /etc/apache2` 
* 编辑httpd.conf: `sudo vi httpd.conf`
* 寻找到这一行`“Include/private/etc/apache2/extra/httpd-dav.conf”` 取消注释，也就是去掉这一行前面的#
* wq保存，退出

###3.修改WebDAV模块的配置文件
到现在位置，WebDAV模块已经被打开了，这样下次apache Web Server启动的时候，就会自己寻找启动WebDAV模块的指令。

* 打开终端，转到extra文件夹：`cd etc/apache2/extra`
* 编辑httpd-dav.conf文件： `sudo vi http-dav.conf`
* 使用下面的配置，取代已经存在的alias项和文件夹配置

![httpd-dav image](/images/2014-01-16-02.jpeg)

###4.创建WebDAV用户
在第三步中，我们制定了一个密码文件** /usr/webdav.passwd ** 他就是用于**WebDAV**模块验证用户的登陆验证；同时，我们制定了一个用户**myMacMini**；

所以，我们需要创建一个用户 **myMacMini**

* 打开终端，转到extra文件夹：  `cd /etx/apache2/extra`
* 创建新的WebDAV用户名 **myMacMini** :  `sudo htpasswd -c /usr/webdav.passwd myMacMini`

###5.创建对应的WebDAV文件夹，并设定相应的权限
在第三步的配置中，我们也设定了WebDAV文件夹的位置 **、Library/WebServer/WebDAV**；

所以，我们需要创建相应的文件夹路径：

* 打开终端，转到extra文件夹： `cd /etc/apache2/extra`
* 创建WebDAV文件夹:

```
	sudo mkdir -p /Library/WebServer/WebDAV
	sudo mkdir -p /usr/var
```
* 设置适当的权限(也可以根据自己的需要，到相应的目录去设定权限)

```
	sudo chown -R www:www /Library/WebServer/WebDAV
	sudo chown -R www:www /usr/var
	sudo chgrp www /usr/webdav.passwd
```

####设置完成，重启apache server:	`sudo apachectl restart`
-----------


####OK,现在你可以尝试用一个WebDAV客户端，去连接你的WebDAV服务器，看看是不是能够正常查看和分享文件了？
* 服务器地址： `http://你的ip地址/webdav/`
* 用户名   ： `你刚才设定的id （我们刚才设置的是myMacMini）`   
* 密码    ：  `你在创建过程中设置的密码`

附：完整的路径位置

![completePath image](/images/2014-01-16-03.jpeg)

