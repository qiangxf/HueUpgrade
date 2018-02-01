###一，安装环境
CentOS6.8虚拟机三台
每台内存1G
每台硬盘50G
###二，需要的安装包
**CDH5.9.0安装包**
**里面包含的文件：**
![CDH安装文档截图](http://upload-images.jianshu.io/upload_images/8578269-2456826c26219bbc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**Parcel里面包含的文件：**
![CDH安装文档截图](http://upload-images.jianshu.io/upload_images/8578269-60112a8cc2bb8083.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**rpms里面包含的文件：**
![CDH安装文档截图](http://upload-images.jianshu.io/upload_images/8578269-aba32fd3e1a02a71.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
首先要确认这些文件数量正确，才能开始下面的安装
###三，安装步骤
首先，将第二部中的安装包挪到各个节点下，这边我是三台主机，划分成为了一个主节点，两个从节点。建议下面操作使用root用户
1修改hostName
设置的host修改服务器的`hostname` ，让`hostname`立刻生效
```
vi /etc/sysconfig/network
```
修改原`hostname`为`newname` ,` reboot`重启。
开机以后，检查`hostname`：
```
uname -a
```
2 添加host名称
首先，使用ifconfig查看每台机子的ip地址，然后：
```
vi /etc/hosts
``` 
在hosts文件中添加每台机子对应的主机名和ip地址，例如：
```
192.168.109.128 master
192.168.109.129 slave1
192.168.109.130 slave2
```
修改完成之后，使用ping命令测试：
```
ping master
ping slave1
ping slave2
```
如果都可以ping成功的话，表示配置没有问题
3设置免密钥登录

第一步:在本地机器上使用ssh-keygen产生公钥私钥对
```
ssh-keygen
```
第二步:用ssh-copy-id将公钥复制到远程机器中
```
ssh-copy-id -i ~/.ssh/id_rsa.pub [用户名@](mailto:hucom@192.168.xx.xx)192.168.x.xx
```
第三步: 登录到远程机器不用输入密码
```
ssh 用户名@192.168.x.xxx
```
如果我们使用master@master可以不用输入密码就可以进入的话，说明我们配置成功

4关闭SELinux
```
vi /etc/selinux/config
```
修改`SELinux=disabled`

5关闭防火墙并设置开机后也关闭
```
service iptables stop

chkconfig iptables off
```
查询防火墙状态:
```
service iptables status
```
6、安装ntp服务
```
yum -y install ntp

chkconfig ntpd on
```
7、重启
```
reboot
```
8、重启之后需要检查的服务：
```
service ntpd status

/usr/sbin/sestatus -v

service iptables status
```
9、复制cdh的软件源到/etc/yum.repos.d/文件夹下（所有节点）
```
cp cloudera-manager.repo /etc/yum.repos.d/
```
10、清理缓存并列出可用的rpm包，如果版本和自己安装的不符，需要注意（所有节点）
```
yum clean all 

yum list | grep cloudera
```
11、进入rpm文件夹，所有节点安装rpm文件（所有节点）
```
yum install *.rpm
```
12、将Parcel的三个文件复制到/opt/cloudera/parcel-repo（所有节点）
```
CDH-5.9.0-1.cdh5.9.0.p0.23-el6.parcel

CDH-5.9.0-1.cdh5.9.0.p0.23-el6.parcel.sha

manifest.json
```
13 安装cloudera-manager-installer.bin（主节点）

给权限：
```
chmod +x ./cloudera-manager-installer.bin
```
执行：
```
./cloudera-manager-installer.bin
```
安装时会提示一个/etc/cloudera-scm-server/db.properties文件存在，找到对应的路径修改文件名为db.properties.bak(做备份)，再次执行即可。

可以用这个命令查看server的启动过程：
```
tail -f /var/log/cloudera-scm-server/cloudera-scm-server.log
```
出现这个说明启动成功：
```
Started Jetty server
```
7、安装cloudera-manager-installer.bin完成后，访问
<http://master:7180>
用户/密码:
```
admin/admin
```
14安装mysql数据库（只给主节点安装）

http://blog.csdn.net/jeffleo/article/details/53559712

启动时报错，可以试试`mysql_install_db`再`service mysqld restart`

15修改数据库密码
```
use mysql;

update user set password=password('root') where user='root';

update mysql.user set authentication_string=password('root') where user='root';

flush privileges;
```
16给数据库赋权限：
```
create database hue DEFAULT CHARACTER SET utf8;

grant all on hue.* TO 'root'@'%' IDENTIFIED BY 'root';

flush privileges;

create database hive DEFAULT CHARACTER SET utf8;

grant all on hive.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;

flush privileges;

create database oozie DEFAULT CHARACTER SET utf8;

grant all on oozie.* TO 'root'@'%' IDENTIFIED BY 'root';

flush privileges;
```
17将mysql的数据库驱动放到`/usr/share/java`目录下