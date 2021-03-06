# Vagrant 指南

---

```json
Vagrant 是一款用来构建虚拟开发环境的工具，非常适合 php/python/ruby/java 这类语言开发 web 应用，“代码在我机子上运行没有问题”这种说辞将成为历史。
```


## Github Stars & Recipes

> 1. [NREL / vagrant-boxes](https://github.com/NREL/vagrant-boxes)
> 2. [lj2007331 / lnmp](https://github.com/lj2007331/lnmp)
> 3. [Vagrantbox.es](http://www.vagrantbox.es/)
> 4. [使用 Vagrant 打造跨平台开发环境](http://segmentfault.com/a/1190000000264347)
> 5. [vagrant 简明使用方法](http://my.oschina.net/guanyue/blog/390287)
> 6. [Vagrant简介和安装配置](http://rmingwang.com/vagrant-commands-and-config.html)
> 7. [用 Vagrant 管理虚拟机](http://ninghao.net/blog/2077)
> 8. [OneinStack – 一键PHP/JAVA安装工具](http://oneinstack.com/)

##开发环境推荐配置

##OpenSource Soteware

+ CentOS-6.5(CentOS-6.5-x86_64-v20140504.box)
+ Vagrant-1.7.4
+ VirualBox-5.0.8 r103449
+ Mac(Unix)\Linux
+ MySQL-5.6.28
+ php-5.6.16
+ Nginx1.9.9
+ phpMyAdmin-4.4.15.1
+ memcached-1.4.25
+ redis-3.0.5

###Step

#### [Vagrant Base Box Downloads](http://nrel.github.io/vagrant-boxes/)
#### Add boxes
```json
 $ vagrant box add {title} {url}
 $ vagrant init {title}
 $ vagrant up
```

See installed vagrant box and remove it
```json
$ vagrant halt
$ vagrant destroy
$ vagrant box list
$ vagrant box remove chehubao
```

Vagrant add box
```json
 $ vagrant box add weipei_develop_server https://developer.nrel.gov/downloads/vagrant-boxes/CentOS-6.7-x86_64-v20151108.box
 # Add packages
 $ vagrant box add weipei_develop_server packages.box
```

Vagrant startup
```json
$ vagrant init weipei_develop_server
$ vagrant up
```


####[安装应用服务](http://oneinstack.com/)

```json
$sudo yum -y install wget screen python
```
使用下载工具下载 [OneinStack – 一键PHP/JAVA安装工具](http://mirrors.linuxeye.com/oneinstack-full.tar.gz)，将安装包分发到服务器

解压安装包
```json
$tar xvzf oneinstack-full.tar.gz cd oneinstack
```
如果需要修改目录(安装、数据存储、Nginx日志)，请修改options.conf文件

```json
# web directory, you can customize
wwwroot_dir=/mnt/www

# nginx Generate a log storage directory, you can freely specify.
wwwlogs_dir=/mnt/wwwlogs
```

```json
$sudo passwd root
$su root
$./install.sh     #请勿sh install.sh或者bash install.sh这样执行
```

####Installed Tools
+ MySQL-5.6.28
+ php-5.6.16
+ Nginx1.9.9
+ phpMyAdmin-4.4.15.1
+ memcached-1.4.25
+ redis-3.0.5

```json
####################Congratulations########################

Nginx/Tengine install dir:      /usr/local/nginx

Database install dir:           /usr/local/mysql
Database data dir:              /data/mysql
Database user:                  root
Database password:              123456@a

PHP install dir:                /usr/local/php
Opcache Control Panel url:      http://10.0.2.15/ocp.php

phpMyAdmin dir:                 /mnt/www/default/phpMyAdmin
phpMyAdmin Control Panel url:   http://10.0.2.15/phpMyAdmin

redis install dir:              /usr/local/redis

memcached install dir:          /usr/local/memcached

index url:                      http://10.0.2.15/
```
####虚拟机重写规则配置
http://oneinstack.com/question/785/
```bash
add content for nginx.conf
fastcgi_split_path_info ^(.+\.php)(/.*)$;
fastcgi_param PATH_INFO $fastcgi_path_info;
```

####OneinStack如何配置MySQL远程连接
http://oneinstack.com/question/oneinstack-how-to-configure-mysql-remote-connection/
为了安全考虑，OneinStack仅允许云主机本机（localhost）连接数据库，如果需要远程连接数据库，需要如下操作：
打开iptables 3306端口
```bash
iptables -I INPUT 4 -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT
service iptables save #保存iptables规则
```
如下图：

数据库授权
远程连接新建一个帐号（帐号名不能为root）。
如：添加一个用户名为linuxeye，密码为123456，授权为% （%表示所有ip能连接，可以设置指定ip）对oneinstack数据库所有权限，命令如下:
```bash
mysql –uroot –p
MySQL [(none)]> grant all privileges on oneinstack.* to linuxeye@'%' identified by '123456'; #授权语句，特别注意有分号
MySQL [(none)]> flush privileges;
MySQL [(none)]> exit; #退出数据库控制台，特别注意有分号
```
如下图：




####[管理服务](http://oneinstack.com/install/)


##问题集锦

### [Fixed the bug on Mac](http://stackoverflow.com/questions/22717428/vagrant-error-failed-to-mount-folders-in-linux-guest)

```
Failed to mount folders in Linux guest. This is usually because
the "vboxsf" file system is not available. Please verify that
the guest additions are properly installed in the guest and
can work properly. The command attempted was:

mount -t vboxsf -o uid=`id -u vagrant`,gid=`getent group vagrant | cut -d: -f3` vagrant /vagrant
mount -t vboxsf -o uid=`id -u vagrant`,gid=`id -g vagrant` vagrant /vagrant

The error output from the last command was:

/sbin/mount.vboxsf: mounting failed with the error: No such device

```

### ARPCHECK=no /sbin/ifup eth1 2> /dev/null
```

The following SSH command responded with a non-zero exit status.
Vagrant assumes that this means the command failed!

ARPCHECK=no /sbin/ifup eth1 2> /dev/null

Stdout from the command:

Device eth1 does not seem to be present, delaying initialization.


Stderr from the command:
```
Fixed the bug
```
sudo rm -rf /etc/udev/rules.d/70-persistent-net.rules
```

###[Symfony\Component\Process\Exception\RuntimeException]
  The Process class relies on proc_open, which is not available on your PHP installation.

Edit your php.ini file and search for the line
 ```json
 ;disable_functions = passthru,exec,system,chroot,chgrp,chown,shell_exec,proc_open,proc_get_status,ini_alter,ini_restore,dl,openlog,syslog,readlink,symlink,popepassthru,stream_socket_server,fsocket,popen
 ```
It should be disabled there and you must enabled it. This a security precaution

###No supported encrypter found. The cipher and / or key length are invalid.

终端使用如下命令：
```json
php artisan key:generate
```
将生成的key复制到config/app.php替换83行的APP_KEY键值。


##Vagrant 推荐配置Vagrantfle

```
config.vm.network "private_network", ip: "192.168.33.10"

config.vm.synced_folder "/Users/chenghuiyong/Applications/server", "/mnt/www",:mount_options => ["dmode=777","fmode=777"]

config.ssh.username = "vagrant"
config.ssh.password = "vagrant"
```
