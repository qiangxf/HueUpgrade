### 首先，确定我们的两个安装包：
* hue.zip（这个包也可以从我们的git仓库下载，具体链接我会发给你）
* huetool.zip

### 然后，在CDH的管理界面停止hue的服务：

1，进入hue

2，选择实例

3，选择Hue Server

4，选择“操作”-->“停止此Hue Server”

### 停止了hue服务以后，进入shell，开始安装kerberos：

1，解压huetool.zip

2，进入，打开krb5.conf

3，修改kdc，admin_server
```
[realms]
SUNMNET.COM = {
  kdc = master
  admin_server = master
 }
```
4，新增两个文件：master和slaves（这两个文件分别存储主节点的主机名和子节点的主机名）：

master输入：
```
master
```
slaves输入：
```
slave1
slave2
slave3
slave4
```
5，进入/usr/java/jdk1.7.0_71/jre/lib/security/这个路径下，将huetool中的
```
local_policy.jar  US_export_policy.jar
```
这两个jar包挪到这个路径下

6，进入huetool文件夹，执行depoly_kerberos.sh脚本（脚本只需要在主节点上执行，建议在master节点上执行）：
```
sh depoly_kerberos.sh
```
7，安装时会提示：
```
Initializing database '/var/kerberos/krb5kdc/principal' for realm 'SUNMNET.COM',
master key name 'K/M@SUNMNET.COM'
You will be prompted for the database Master Password.
It is important that you NOT FORGET this password.
Enter KDC database master key:
Re-enter KDC database master key to verify:
```
输入密码：
```
sunmnet123
```
8，在CDH中启动Kerberos：

进入CDH的管理界面-->“管理”-->“安全”-->“启用Kerberos服务”

第一个页面：

* 第一行选择：MIT KDC

* 第二行填写：master

* 第三行填写：SUNMNET.COM

* 第四行填写：aes256-cts

* 第五行选择：5天

第二个页面：

* 第一行：root/admin@SUNMNET.COM

* 第二行：sunmnet123（这里是我们刚才设置的密码，建议设置为sunmnet123）

第三个页面：

* 勾选

第四个页面：

* 全默认

第五个页面：

* 全默认

然后就完成了Kerberos的安装

### 测试Kerberos

第一次输入：
```
hadoop fs -ls /
```
会报错，然后我们输入：
```
kinit hdfs
```
会提示我们输入密码：
```
sunmnet123
```
然后再输入：
```
hadoop fs -ls /
```
这个时候就不会报错了
### 安装openLDAP

1，安装migrationtools：
```
yum install migrationtools -y
```
2，修改默认配置：
```
vim /usr/share/migrationtools/migrate_common.ph

#修改两行数据

# 第71行：

DEFAULT_MAIL_DOMAIN = "sunmnet.com";

#第74行

$DEFAULT_BASE = "dc=sunmnet,dc=com";
```
3，修改完成之后，执行脚本（只在主节点上执行，建议在master上执行）：
```
sh depoly_ldap.sh
```
### 安装相关依赖

1，yum安装依赖
```
yum install ant asciidoc cyrus-sasl-devel cyrus-sasl-gssapi cyrus-sasl-plain gcc gcc-c++ krb5-devel libffi-devel libxml2-devel libxslt-devel make mysql mysql-devel openldap-devel python-devel sqlite-devel gmp-devel
```
2，安装maven
```
cd /usr/local/src/

wget http://mirrors.hust.edu.cn/apache/maven/maven-3/3.1.1/binaries/apache-maven-3.1.1-bin.tar.gz

tar zxf apache-maven-3.1.1-bin.tar.gz

mv apache-maven-3.1.1 /usr/local/maven3

再配置相关的环境变量

vi /etc/profile

#在适当的位置添加

export M2_HOME=/usr/local/maven3

export PATH=$PATH:$JAVA_HOME/bin:$M2_HOME/bin

最后保存并使配置生效

source /etc/profile

验证mvn

mvn -v
```
3，安装git

[Centos6.8安装git](http://blog.csdn.net/u013372487/article/details/51263093)

### 安装Sentry

1，进入CDH的主界面，选择“添加服务”-->“Sentry服务”

2，新建一个Sentry数据库：
```
create database sentry DEFAULT CHARACTER SET utf8;

grant all on sentry.* TO 'root'@'%' IDENTIFIED BY 'root';

flush privileges;
```
3，下一步下一步完成安装

### 安装Sqoop2

安装方式和安装Sentry同理，不过不需要创建数据库

### 完成以上工作之后，进行以下操作（按顺序操作，一定都要按照顺序执行）：

[配置相关服务](https://github.com/liutianyuan/CDH/blob/master/CDH%E5%90%84%E7%A7%8D%E7%BB%84%E4%BB%B6%E9%9B%86%E6%88%90%E8%AE%A4%E8%AF%81%E5%92%8C%E6%8E%88%E6%9D%83%E7%9B%B8%E5%85%B3%E9%85%8D%E7%BD%AE.md)

执行上述在每个服务的配置中执行

补充上述操作：

hue的配置中搜索：sqoop

sqoop服务选择sqoop2

### 编译

执行上述所有操作之后，进入
```
/opt/cloudera/parcels/CDH-5.9.0-1.cdh5.9.0.p0.23/lib/hue
```
然后将hue文件夹备份，并clone我们的hue
```
mv hue hue.bak

git clone hue
```
clone完成之后，进入hue文件夹，执行
```
make apps

build/env/bin/python tools/app_reg/app_reg.py --install apps/collectioninfo

build/env/bin/python tools/app_reg/app_reg.py --install apps/columnencrypt

build/env/bin/python tools/app_reg/app_reg.py --install apps/httprequest
```
### 登录hue

进入CDH的管理界面，选择hue，然后选择hueWEBUI，然后进入我们的hue界面

用户名：ladpadmin，密码：sunmnet123