# 基本操作

/usr/local/Cellar/mysql/8.0.29_1

/usr/local/etc/my.cnf

```bash
lsof -i -P | grep -i mysqld
```

##### 启动

```bash
brew services start mysql
```

##### 重启

```bash
brew services restart mysql
```

##### 重设密码

```bash
mysql_secure_installation
```

##### 登录

```bash
mysql -uroot -p
```

##### 进入安全模式

```bash
mysqld_safe --skip-grant-tables
```

##### 更新 root 密码

```mysql
UPDATE mysql.user SET authentication_string=PASSWORD('') WHERE User='root';

UPDATE mysql.user SET Password=PASSWORD('root') WHERE User='root';

FLUSH PRIVILEGES;
SET PASSWORD FOR 'root'@'0.0.0.0' = 'root';
ALTER user 'root'@'0.0.0.0' IDENTIFIED BY 'root';

SET PASSWORD FOR 'root'@'localhost' = 'password';
```



```mysql
show global variables like 'port';
```

