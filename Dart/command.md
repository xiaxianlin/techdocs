# 命令行
#### 命令形式
```shell
dart <command> <...args>
```
当command为空时，相当于run命令

#### analyze
分析项目源码。可以使用dartanalyzer替代。
```shell
dart analyze [<DIRECTORY|DART_FILE>]
```

#### compile
可以执行方式编译dart代码。可以使用dart2js和dart2native替代。
```shell
dart compile exe <DART_FILE>
```

#### create
创建一个新项目。可以使用后stagehand代替。
```shell
dart create <DIRECTORY>
```

#### fix
自动修复dart的源码。可以使用dartfix替代。
```shell
dart fix [<DIRECTORY|DART_FILE>]
```

#### format
格式化dart代码。可以使用dartfmt替代。
```shell
dart format [<DIRECTORY|DART_FILE>]
```

#### migrate
支持代码迁移成空安全模式。
```shell
dart migrate
```

#### pub
包管理。可以使用pub替代。
```shell
dart pub <PUB_COMMAND>
```

#### run
运行一个dart项目。
```shell
dart run [options] [<DART_FILE> | <PACKAGE_TARGET>] [args]
```

**options**
- -h, --help 打印帮助信息
- --observe=<[<port>[/<bind-address>]]> 开启dart调试
- --enable-vm-service=<[<port>[/<bind-address>]]> 启用虚拟机服务并监听一个特殊端口
	- --[no-]pause-isolates-on-exit 在退出时暂停isolates
	- --[no-]pause-isolates-on-unhandled-exceptions 遇到一个未处理的异常时暂停isolates
	- --[no-]warn-on-pause-with-no-debugger 当一个isolate没有被附加在调试器上时打印一个警告
- --verbosity 设置编译的详细级别
	- [all] (default) 显示所有信息
	- [error] 只显示error信息
	- [info] 显示error，warning和info信息
	- [warning] 只显示error和warning信息
- -D, --define=<key=value> 定义一个环境描述

#### test
运行包内的单元测试。可以用**pub run test**替代。
```shell
dart test [<DIRECTORY|DART_FILE>]
```
