### LDAP�����벻��ȷ��

�����޸����룺
```
ldappasswd -x -D 'uid=ldapadmin,ou=people,dc=sunmnet,dc=com' -w sunmnet123 'uid=ldapadmin,ou=people,dc=sunmnet,dc=com' -S
```
### ����hue��ɺ��hive���в��ԣ�
```
��HDFSȨ�ޣ�

kinit hdfs

�������룺

sunmnet123

beeline -u "jdbc:hive2://master:10000/default" -n ldapadmin -p sunmnet123

show databases;

ֻ��ʾһ���⣺
+----------------+--+
| database_name  |
+----------------+--+
| default        |
+----------------+--+

Ȼ����ʱ��Ҳ��֪����ô�������
```

### hue��logλ�ã�
```
/var/log/hue
```