### ���ȣ�ȷ�����ǵ�������װ����
* hue.zip�������Ҳ���Դ����ǵ�git�ֿ����أ����������һᷢ���㣩
* huetool.zip

### Ȼ����CDH�Ĺ������ֹͣhue�ķ���

1������hue

2��ѡ��ʵ��

3��ѡ��Hue Server

4��ѡ�񡰲�����-->��ֹͣ��Hue Server��

### ֹͣ��hue�����Ժ󣬽���shell����ʼ��װkerberos��

1����ѹhuetool.zip

2�����룬��krb5.conf

3���޸�kdc��admin_server
```
[realms]
SUNMNET.COM = {
  kdc = master
  admin_server = master
 }
```
4�����������ļ���master��slaves���������ļ��ֱ�洢���ڵ�����������ӽڵ������������

master���룺
```
master
```
slaves���룺
```
slave1
slave2
slave3
slave4
```
5������/usr/java/jdk1.7.0_71/jre/lib/security/���·���£���huetool�е�
```
local_policy.jar  US_export_policy.jar
```
������jar��Ų�����·����

6������huetool�ļ��У�ִ��depoly_kerberos.sh�ű����ű�ֻ��Ҫ�����ڵ���ִ�У�������master�ڵ���ִ�У���
```
sh depoly_kerberos.sh
```
7����װʱ����ʾ��
```
Initializing database '/var/kerberos/krb5kdc/principal' for realm 'SUNMNET.COM',
master key name 'K/M@SUNMNET.COM'
You will be prompted for the database Master Password.
It is important that you NOT FORGET this password.
Enter KDC database master key:
Re-enter KDC database master key to verify:
```
�������룺
```
sunmnet123
```
8����CDH������Kerberos��

����CDH�Ĺ������-->������-->����ȫ��-->������Kerberos����

��һ��ҳ�棺

* ��һ��ѡ��MIT KDC

* �ڶ�����д��master

* ��������д��SUNMNET.COM

* ��������д��aes256-cts

* ������ѡ��5��

�ڶ���ҳ�棺

* ��һ�У�root/admin@SUNMNET.COM

* �ڶ��У�sunmnet123�����������Ǹղ����õ����룬��������Ϊsunmnet123��

������ҳ�棺

* ��ѡ

���ĸ�ҳ�棺

* ȫĬ��

�����ҳ�棺

* ȫĬ��

Ȼ��������Kerberos�İ�װ

### ����Kerberos

��һ�����룺
```
hadoop fs -ls /
```
�ᱨ��Ȼ���������룺
```
kinit hdfs
```
����ʾ�����������룺
```
sunmnet123
```
Ȼ�������룺
```
hadoop fs -ls /
```
���ʱ��Ͳ��ᱨ����
### ��װopenLDAP

1����װmigrationtools��
```
yum install migrationtools -y
```
2���޸�Ĭ�����ã�
```
vim /usr/share/migrationtools/migrate_common.ph

#�޸���������

# ��71�У�

DEFAULT_MAIL_DOMAIN = "sunmnet.com";

#��74��

$DEFAULT_BASE = "dc=sunmnet,dc=com";
```
3���޸����֮��ִ�нű���ֻ�����ڵ���ִ�У�������master��ִ�У���
```
sh depoly_ldap.sh
```
### ��װ�������

1��yum��װ����
```
yum install ant asciidoc cyrus-sasl-devel cyrus-sasl-gssapi cyrus-sasl-plain gcc gcc-c++ krb5-devel libffi-devel libxml2-devel libxslt-devel make mysql mysql-devel openldap-devel python-devel sqlite-devel gmp-devel
```
2����װmaven
```
cd /usr/local/src/

wget http://mirrors.hust.edu.cn/apache/maven/maven-3/3.1.1/binaries/apache-maven-3.1.1-bin.tar.gz

tar zxf apache-maven-3.1.1-bin.tar.gz

mv apache-maven-3.1.1 /usr/local/maven3

��������صĻ�������

vi /etc/profile

#���ʵ���λ�����

export M2_HOME=/usr/local/maven3

export PATH=$PATH:$JAVA_HOME/bin:$M2_HOME/bin

��󱣴沢ʹ������Ч

source /etc/profile

��֤mvn

mvn -v
```
3����װgit

[Centos6.8��װgit](http://blog.csdn.net/u013372487/article/details/51263093)

### ��װSentry

1������CDH�������棬ѡ����ӷ���-->��Sentry����

2���½�һ��Sentry���ݿ⣺
```
create database sentry DEFAULT CHARACTER SET utf8;

grant all on sentry.* TO 'root'@'%' IDENTIFIED BY 'root';

flush privileges;
```
3����һ����һ����ɰ�װ

### ��װSqoop2

��װ��ʽ�Ͱ�װSentryͬ����������Ҫ�������ݿ�

### ������Ϲ���֮�󣬽������²�������˳�������һ����Ҫ����˳��ִ�У���

[������ط���](https://github.com/liutianyuan/CDH/blob/master/CDH%E5%90%84%E7%A7%8D%E7%BB%84%E4%BB%B6%E9%9B%86%E6%88%90%E8%AE%A4%E8%AF%81%E5%92%8C%E6%8E%88%E6%9D%83%E7%9B%B8%E5%85%B3%E9%85%8D%E7%BD%AE.md)

ִ��������ÿ�������������ִ��

��������������

hue��������������sqoop

sqoop����ѡ��sqoop2

### ����

ִ���������в���֮�󣬽���
```
/opt/cloudera/parcels/CDH-5.9.0-1.cdh5.9.0.p0.23/lib/hue
```
Ȼ��hue�ļ��б��ݣ���clone���ǵ�hue
```
mv hue hue.bak

git clone hue
```
clone���֮�󣬽���hue�ļ��У�ִ��
```
make apps

build/env/bin/python tools/app_reg/app_reg.py --install apps/collectioninfo

build/env/bin/python tools/app_reg/app_reg.py --install apps/columnencrypt

build/env/bin/python tools/app_reg/app_reg.py --install apps/httprequest
```
### ��¼hue

����CDH�Ĺ�����棬ѡ��hue��Ȼ��ѡ��hueWEBUI��Ȼ��������ǵ�hue����

�û�����ladpadmin�����룺sunmnet123