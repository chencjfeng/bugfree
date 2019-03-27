###导语：
> 最近公司需求，需要部署一个测试case录入系统、bug记录系统，而同事推荐之前使用过的BugFree系统，这个由淘宝开发的开源系统，但已经在2013年就停止更新了，那么我们介绍下如何部署。
<br>
###1.准备环境
①、CentOS-7-x86_64-Minimal-1708系统：http://59.80.44.100/isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-1810.iso
②、BugFree系统源码：[https://github.com/chencjfeng/bugfree](https://github.com/chencjfeng/bugfree)

③、yum源更新，并停止防火墙
~~~
yum  install epel-release  //扩展包更新包
yum  update //更新yum源
systemctl stop firewalld.service //停止防火墙服务
systemctl disable firewalld.service //禁用防火墙开机启动服务
~~~
④、下载更新mysql-server源
~~~
yum -y install wget
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
rpm -ivh mysql-community-release-el7-5.noarch.rpm
~~~

###2.配置Apache环境
①、安装Apache
~~~
yum install httpd
~~~
②、启动进程
~~~
service httpd start
~~~
③、设置httpd开机启动
~~~
chkconfig httpd on
~~~
访问服务器ip，能出现以下页面则表示安装成功，不能出现以下页面则排查下防火墙是否关闭和httpd服务是否起来
![Apache](https://upload-images.jianshu.io/upload_images/4970496-5b4538c9e0823f57.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)


###3.配置mysql环境
①、安装mysql
~~~
yum install mysql mysql-server
~~~
②、启动进程
~~~
service mysqld start
~~~
③、配置mysql root初始密码
~~~
mysql

use mysql

update user set password=password('密码') where user='root' ;   //此句结尾需加上分号，分号不能漏掉
~~~
④、重启mysql服务生效
~~~
service mysqld restart
~~~
⑤、验证密码修改是否成功
~~~
mysql -u root -p
~~~
然后输入新的密码，如成功登陆，则表明新密码已经生效。
⑥、设置mysqld开机启动
~~~
chkconfig mysqld on
~~~

###4.安装php服务器
~~~
yum install php php-mysql php-gd php-imap php-ldap php-odbc php-pear php-xml php-xmlrpc
yum install libmcrypt
yum install php-mcrypt
~~~

###5.安装bugFree
①、将bugFree源码解压，放置/var/www/html/目录下，并重命名文件夹名字为“bugfree”,如图：
![bugfree](https://upload-images.jianshu.io/upload_images/4970496-5d73c2016cf37227.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)
②、创建BugFile文件夹，在/var/www/html/目录下
~~~
cd /var/www/html/
mkdir BugFile
~~~
③、配置读写权限，创建缺失文件夹
~~~
mkdir /var/www/html/bugfree/assets
mkdir /var/www/html/bugfree/protected/runtime
chmod 777 /var/www/html/BugFile
chmod 777 /var/www/html/bugfree/assets
chmod 777 /var/www/html/bugfree/protected/runtime
chmod -R 777 /var/www/html/bugfree/protected/config
chmod -R 777 /var/www/html/bugfree/install
~~~
④、关闭selinux（不关闭的话，Apache用户对/var/目录下其他东西权限还是不可写）
———临时关闭———
~~~
setenforce 0
~~~
———永久关闭———
修改/etc/selinux/config文件
将SELINUX=enforcing改为SELINUX=disabled
保存退出重启机器

###6.配置bugfree系统环境
①、打开“http://ip地址/bufree/install”链接（例：http://192.168.1.228/bugfree/install），如下图，文件权限都OK没问题，点击继续。
![步骤一](https://upload-images.jianshu.io/upload_images/4970496-5a8ceb2d471105aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)
②、填写数据库账户和密码，root账户，密码是mysql设置的初始密码，点击安装。
![步骤二](https://upload-images.jianshu.io/upload_images/4970496-264c75f118e55873.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)
③、安装完成，进入Bugfree系统。
![步骤三](https://upload-images.jianshu.io/upload_images/4970496-2e3ee9c275f85647.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

###7.修复3.0.4 bugfree系统执行case出现500的问题
将“/var/www/html/bugfree/protected/extensions/simple_html_dom.php”中第988行代码注释掉即可，如图：
![500错误](https://upload-images.jianshu.io/upload_images/4970496-1f653de7cd082693.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)



