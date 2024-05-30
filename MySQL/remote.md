1. 进MySQL之后
2. 输入以下语句，进入mysql库： `use mysql;`
3. 更新域属性，’%’表示允许外部访问： `update user set host='%' where user ='root';`
4. 执行以上语句之后再执行(刷新配置)： `FLUSH PRIVILEGES;`
5. 再执行授权语句： `GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'WITH GRANT OPTION;`

### 修改配置

`vim /usr/local/etc/mongod.conf`

```conf
[mysqld]
# Only allow connections from localhost
bind-address = 0.0.0.0
mysqlx-bind-address = 0.0.0.0
```

### 重启 MySQL

```shell
brew services restart mongodb-community
```