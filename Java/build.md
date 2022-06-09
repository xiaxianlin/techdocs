# Java打包以及部署
## SpringBoot
```shell
# 本地打包安装
mvn clean
mvn install

# 上传到指定目录
scp target/*.har user@ip:<dir>

# 后台运行
nohup java -Dfile.encoding=UTF-8 -jar <jar> > log.log &
```
