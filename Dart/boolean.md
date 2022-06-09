# 布尔
布尔值只有2个实例true和false。该类无法进行方法扩展。一般属于比较后的返回值。

## bool
提供布尔值处理的类。

#### 构造函数
**bool.fromEnvironment(String name, {bool defaultValue = false})**
获取指定名称的环境变量的值，如果没有该环境变量，则返回默认值。
```dart
// 执行时使用命令：dart run --define=enableLog=true ...
book.fromEnviroment("enableLog"); // true
```

**bool.hasEnvironment(String name)**
判断是否存在指定名称的环境变量。
```dart
// 执行时使用命令：dart run --define=enableLog=true ...
book.hasEnviroment("enableLog"); // true
```

#### 属性
**hashCode -> int**
bool值的hashCode是固定值。
```dart
true.hashCode; // 1231
false.hashCode; // 1237
```

#### 方法
**toString() -> String**
bool值转成字符串。
```dart
true.toString(); // 'true'
false.toString(); // 'false'
```
