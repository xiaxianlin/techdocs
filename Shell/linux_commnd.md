# Linux常用命令
## 查看文件夹大小
```shell
du -h --max-depth=1 <dir>
```

## 查询当前监听端口的运行程序
```shell
lsof -n -i4TCP:<port> | grep LISTEN
```

## 删除文件夹所有的node_modules
```shell
find . -name 'node_modules' -type d -prune -exec rm -rf '{}' +
```
