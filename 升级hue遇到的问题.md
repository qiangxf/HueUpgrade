### LDAP的密码不正确：

尝试修改密码：
```
ldappasswd -x -D 'uid=ldapadmin,ou=people,dc=sunmnet,dc=com' -w sunmnet123 'uid=ldapadmin,ou=people,dc=sunmnet,dc=com' -S
```
### 升级hue完成后对hive进行测试：
```
给HDFS权限：

kinit hdfs

输入密码：

sunmnet123

beeline -u "jdbc:hive2://master:10000/default" -n ldapadmin -p sunmnet123

show databases;

只显示一个库：
+----------------+--+
| database_name  |
+----------------+--+
| default        |
+----------------+--+

然后暂时我也不知道怎么解决。。
```

### hue的log位置：
```
/var/log/hue
```