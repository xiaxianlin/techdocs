## MongoDB基础命令

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



