###һ����װ����
CentOS6.8�������̨
ÿ̨�ڴ�1G
ÿ̨Ӳ��50G
###������Ҫ�İ�װ��
**CDH5.9.0��װ��**
**����������ļ���**
![CDH��װ�ĵ���ͼ](http://upload-images.jianshu.io/upload_images/8578269-2456826c26219bbc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**Parcel����������ļ���**
![CDH��װ�ĵ���ͼ](http://upload-images.jianshu.io/upload_images/8578269-60112a8cc2bb8083.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**rpms����������ļ���**
![CDH��װ�ĵ���ͼ](http://upload-images.jianshu.io/upload_images/8578269-aba32fd3e1a02a71.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
����Ҫȷ����Щ�ļ�������ȷ�����ܿ�ʼ����İ�װ
###������װ����
���ȣ����ڶ����еİ�װ��Ų�������ڵ��£����������̨���������ֳ�Ϊ��һ�����ڵ㣬�����ӽڵ㡣�����������ʹ��root�û�
1�޸�hostName
���õ�host�޸ķ�������`hostname` ����`hostname`������Ч
```
vi /etc/sysconfig/network
```
�޸�ԭ`hostname`Ϊ`newname` ,` reboot`������
�����Ժ󣬼��`hostname`��
```
uname -a
```
2 ���host����
���ȣ�ʹ��ifconfig�鿴ÿ̨���ӵ�ip��ַ��Ȼ��
```
vi /etc/hosts
``` 
��hosts�ļ������ÿ̨���Ӷ�Ӧ����������ip��ַ�����磺
```
192.168.109.128 master
192.168.109.129 slave1
192.168.109.130 slave2
```
�޸����֮��ʹ��ping������ԣ�
```
ping master
ping slave1
ping slave2
```
���������ping�ɹ��Ļ�����ʾ����û������
3��������Կ��¼

��һ��:�ڱ��ػ�����ʹ��ssh-keygen������Կ˽Կ��
```
ssh-keygen
```
�ڶ���:��ssh-copy-id����Կ���Ƶ�Զ�̻�����
```
ssh-copy-id -i ~/.ssh/id_rsa.pub [�û���@](mailto:hucom@192.168.xx.xx)192.168.x.xx
```
������: ��¼��Զ�̻���������������
```
ssh �û���@192.168.x.xxx
```
�������ʹ��master@master���Բ�����������Ϳ��Խ���Ļ���˵���������óɹ�

4�ر�SELinux
```
vi /etc/selinux/config
```
�޸�`SELinux=disabled`

5�رշ���ǽ�����ÿ�����Ҳ�ر�
```
service iptables stop

chkconfig iptables off
```
��ѯ����ǽ״̬:
```
service iptables status
```
6����װntp����
```
yum -y install ntp

chkconfig ntpd on
```
7������
```
reboot
```
8������֮����Ҫ���ķ���
```
service ntpd status

/usr/sbin/sestatus -v

service iptables status
```
9������cdh�����Դ��/etc/yum.repos.d/�ļ����£����нڵ㣩
```
cp cloudera-manager.repo /etc/yum.repos.d/
```
10�������沢�г����õ�rpm��������汾���Լ���װ�Ĳ�������Ҫע�⣨���нڵ㣩
```
yum clean all 

yum list | grep cloudera
```
11������rpm�ļ��У����нڵ㰲װrpm�ļ������нڵ㣩
```
yum install *.rpm
```
12����Parcel�������ļ����Ƶ�/opt/cloudera/parcel-repo�����нڵ㣩
```
CDH-5.9.0-1.cdh5.9.0.p0.23-el6.parcel

CDH-5.9.0-1.cdh5.9.0.p0.23-el6.parcel.sha

manifest.json
```
13 ��װcloudera-manager-installer.bin�����ڵ㣩

��Ȩ�ޣ�
```
chmod +x ./cloudera-manager-installer.bin
```
ִ�У�
```
./cloudera-manager-installer.bin
```
��װʱ����ʾһ��/etc/cloudera-scm-server/db.properties�ļ����ڣ��ҵ���Ӧ��·���޸��ļ���Ϊdb.properties.bak(������)���ٴ�ִ�м��ɡ�

�������������鿴server���������̣�
```
tail -f /var/log/cloudera-scm-server/cloudera-scm-server.log
```
�������˵�������ɹ���
```
Started Jetty server
```
7����װcloudera-manager-installer.bin��ɺ󣬷���
<http://master:7180>
�û�/����:
```
admin/admin
```
14��װmysql���ݿ⣨ֻ�����ڵ㰲װ��

http://blog.csdn.net/jeffleo/article/details/53559712

����ʱ������������`mysql_install_db`��`service mysqld restart`

15�޸����ݿ�����
```
use mysql;

update user set password=password('root') where user='root';

update mysql.user set authentication_string=password('root') where user='root';

flush privileges;
```
16�����ݿ⸳Ȩ�ޣ�
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
17��mysql�����ݿ������ŵ�`/usr/share/java`Ŀ¼��