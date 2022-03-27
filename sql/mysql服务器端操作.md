### 登录mysql

```mysql
mysql -u root（账号） -p
USE root
SHOW TABLES;
```

### 查询所有表

```mysql
Select  table_name from information_schema.tables 
```

### 给某个表添加某个字段

```
ALTER TABLE t_user ADD COLUMN user_age int(11) DEFAULT NULL COMMENT '年龄' AFTER user_email;
```

t_user：表名

user_age：添加的字段名称

int(11)：字段类型和长度

DEFAULT NULL：默认为NULL

'年龄'：字段含义

AFTER user_email：在user_email字段之后添加user_age字段

### 备份表

```
# 本地备份可以不添加端口和主机IP，username、passward是数据库用户名和密码
mysqldump -h *.*.*.* -p 3306 -u username -p password --database mysql > /data/backup/mysql.sql

```

