## MongoDB基础命令

config: /usr/local/etc/mongod.conf

#### 安装

```shell
brew install mongodb-community
brew install mongodb-community-shell
brew install mongodb-database-tools
```



#### 启动

```shell
brew services start mongodb-community
mongod --config /usr/local/etc/mongod.conf
```



#### 停止

```shell
brew services stop mongodb-community
```



#### 卸载

```shell
brew uninstall mongodb-community
brew uninstall mongodb-database-tools
```



##### 登录

```bash
mongo -u "admin" -p "admin122"
```



##### 设置账户密码

```mongo
usea admin;
db.createUser({ user: 'root', pwd: 'root', roles:[{ role: 'root', db: 'admin' }]})

db.createUser({ user: 'test', pwd: '111111', roles: [{ role: 'readWrite', db: 'test' }] })
```



##### 用户权限

readWrite：允许用户读写指定数据库
dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile
userAdmin：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户
clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。
readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限
readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限

userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限

dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。

