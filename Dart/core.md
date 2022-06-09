# 核心
#### 打印
print打印对象的信息，如果对象是非字符串，则调用对象的toString()方法。
```dart
print(object);
```

#### 断言
```dart
assert(true);
```

#### 数值
数值的原型类是num，包含两个子类型整数int和浮点数float，数值包括二进制、八进制、十进制、十六进制以及科学计数法，十六进制由0x开头表示。

**字符串转数值**
```dart
// 整数无法转换浮点数
int.parse("42"); // 42
int.parse("0x42"); // 66
int.parse("19.1"); // FormatException: Invalid radix-10 number (at character 1)

// 整数转换时指定进制
int.parse('42', radix: 16); // 66

// 浮点数是转不了十六进制
double.parse("42"); // 42.0
double.parse("19.1"); // 19.1
double.parse("0x42"); // FormatException: Invalid double
double.parse("1.2e+2"); // "120.0"

// 使用num转换是最安全的，但是需要在使用的时候断言类型
num.parse("42") as int; // 42
num.parse("0x42") as int ; // 66
num.parse("19.1") as double; // 19.1
```

**数值转字符串**
```dart
42.toString(); // "42"
19.1.toString(); // "19.1"
// 十六进制在转成字符串的时候，会先转成整型
0x42.toString(); // "62"
// 浮点型转字符串时指定精度
123.456.toStringAsFixed(2); // "123.45"
// 转成科学计数法
123.456.toStringAsPrecision(2); // "1.2e+2"
```

#### 字符串
字符串在dart里面是一串UTF-16编码的不可变序列。字符串原型类String提供了查询、搜索、修改等一系列基础功能，并可以配合正在表达式进行高阶处理。
```dart
"ab" + "cde"; // abcde
"ab" * 3; // ababab
"ab" == "cde";  // false
```

**查询**
```dart
"abcde".indexOf('a'); // 0
"abcde".contains("bc"); // true
"abcde".startWith("a"); //true
"abcde".endWith("a"); // false
"abcde".substring(2, 4); // "cd"
"abcde".codeUnitAt(1); // 98
```

**修改**
```dart
"the".toUpperCase(); // "THE"
"THE".toLowerCase(); // "the"
" the ".trim(); // "the"
"a good idea".split(" "); // ["a", "good", "idea"]
```

**使用正则**
```dart
"this is a string.".contains(RegExp(r'\d+')); // false
"Current time is 2021-12-17 14:00.".replaceAll(RegExp(r'\d+'), 'XX'); // "Current time is XX-XX-XX XX:XX."
```

**构建字符串**
使用StringBuffer以编码方式生成字符串，在调用toString()之前StringBuffer不会生成新的String对象。
```dart
var sb = StringBuffer();
sb..write('this ')
  ..writeAll(['is', 'a', 'string'], ' ')
  ..write('.');
sb.toString(); // "this is a string."
```

#### 集合
集合包括列表(List)、无序列表(Set)和键值映射(Map)，集合的都实现了迭代的功能，可以调用迭代的方法。

#### 列表(List)
列表可以通过字面量直接声明，也可以List类构造方法构造。
```dart
var data = <String>[];
data.isEmpty(); // true
data.add('dog'); // ['dog']
data.addAll(['cat', 'fox']); // ['dog', 'cat', 'fox']
data.length; // 3
data.indexOf('cat'); // 1
data.removeAt(1); // ['dog', 'fox']
data.clear();

// 使用构造方法
var data = List(); // 这个构造函数以及被遗弃了，建议使用命名构造函数
var numbers = List.filled(5, 0); // [0, 0, 0, 0, 0]
numbers.add("6"); // Error: The argument type 'String' can't be assigned to the parameter type 'int'
```

#### 无序列表(Set)
无序列表是一个没有顺序且元素唯一的集合，由于没有顺序，也就没有根据索引获取元素。
```dart
var data = <String>{};
data.add('gold'); // {'gold'}
data.addAll(['titanium', 'xenon']); // {'gold', 'titanium', 'xenon'}
// 重复添加只会保存一个
data.add('gold'); // {'gold', 'titanium', 'xenon'}
data.length; // 3
data.remove('gold'); // {'titanium', 'xenon'}
data.contains('xenon'); // true
data.containsAll(['titanium', 'xenon']); // true

// 使用构造方法
var data = Set<String>();
var data = Set.from(['titanium', 'xenon']);
```

#### 键值映射(Map)
映射，通常称为字典或哈希，是键值对的无序集合。映射将键与某个值相关联以便于检索。映射的键和值的集合都是迭代。键是唯一的，重复添加会进行值覆盖。
```dart
// 构造方式
var data = Map();
var data = Map<int, String>();
var data = {
	1: "Tom",
	2: "Kelly"
}
data.keys; // (1, 2)
data.values; // (Tom, Kelly)
data.length; // 2 
data[1]; // "Tom"
data.containsKey(1); // true
data.putIfAbsent(3, () => "Jimmy"); // {1: Tom, 2: Kelly, 3: Jimmy}
data.addAll({4: "Henry", 5: "Cathy"}); // {1: Tom, 2: Kelly, 3: Jimmy, 4: Henry, 5: Cathy}
```

#### URI
URI提供一些列方法来编码和解码URL字符串。
```dart
var url = "https://www.baidu.com?s=flutter dart";
var encoded = Uri.encodeFull(url); // https://www.baidu.com?s=flutter%20dart
Uri.decodeFull(encoded); // https://www.baidu.com?s=flutter dart

var encoded = Uri.encodeComponent(url); // https%3A%2F%2Fwww.baidu.com%3Fs%3Dflutter%20dart
Uri.decodeComponent(encoded); // https://www.baidu.com?s=flutter dart

var uri =Uri.parse('https://www.baidu.com/foo/bar#frag');
uri.scheme; // https
uri.host; // www.baidu.com
uri.path; // /foo/bar
uri.fragment; // frag
uri.origin; // https://www.baidu.com

var uri = Uri(
	scheme: 'https',
	host: 'www.baidu.com',
	path: '/foo/bar',
	fragment: 'frag');
uri.toString();  // https://www.baidu.com/foo/bar#frag
```

#### 时间
时间戳是从1970-01-01 00:00:00:000开始计算的。
```dart
// 获取当前时间
DateTime.now(); // 2021-12-20 16:23:48.685248
DateTime(2000); // 2000-01-01 00:00:00.000
DateTime(2000, 1, 2); // 2000-01-02 00:00:00.000
DateTime.utc(2000); // 2000-01-01 00:00:00.000Z
DateTime.fromMillisecondsSinceEpoch(946684800000, isUtc: true); // 2000-01-01 00:00:00.000Z
DateTime.parse('2000-01-01T00:00:00Z'); // 2000-01-01 00:00:00.000Z
DateTime.utc(1970).millisecondsSinceEpoch; // 0

var y2k = DateTime.utc(2000);
var y2001 = y2k.add(const Duration(days: 366));// 2001-01-01 00:00:00.000Z
var december2000 = y2001.subtract(const Duration(days: 30)); // 2000-12-02 00:00:00.000Z
december2000.year; // 2000
december2000.month; // 12

var duration = y2001.difference(y2k);
duration.inDays; // 366
```

#### 功能类
核心提供了一系列功能类，用来实现排序、映射值以及迭代。

**对象比较**
```dart
class Line implements Comparable<Line> {
  final int length;
  const Line(this.length);
  @override
  int compareTo(Line other) => length - other.length;
	@override
	String toString() => length.toString();
}

var l1 = line(1);
var l2 = line(2);
l1.compareTo(l2); // -1

var data = [Line(4), l1, l2];
data.sort(); // [1, 2, 4];

// 修改compareTo(Line other) => other.length - length;
data.sort(); // [4, 2, 1];
```

**迭代**
实现迭代能力需要实现一个迭代器，然后继承IterableBase并注入迭代器来获得迭代的能力，获得迭代能力后，就可以用forin进行遍历。
```dart
// 进度模型
class Process {
  var current = 0;
  Process(this.current);
  @override
  String toString() => current.toString();
}
// 实现进度迭代器
class ProcessIterator implements Iterator<Process> {
  var _i = 0;
  @override
  Process get current => Process(_i);

  @override
  bool moveNext() {
    _i++;
    return _i < 5;
  }
}
// 创建可迭代进度
class Processes extends IterableBase<Process> {
  @override
  Iterator<Process> get iterator => ProcessIterator();
}
// 遍历进度
for (var p in Processes()) {
	print(p);
}
// output:
// 1
// 2
// 3
// 4
```

#### 异常
在核心库中，提供了一些列的常用异常和错误。也可以通过实现Exception来实现自定义异常
```dart
class FooException implements Exception {
  final String? msg;

  const FooException([this.msg]);

  @override
  String toString() => msg ?? 'FooException';
}

throw FooException(); 
// Unhandled exception:
// FooException
```

#### 异步
在dart中使用Future和Stream来实现异步编程，其中Future类似Promise一样，在未来的某个时间返回值。而Stream提供了一种方式获取一个序列值。异步编程需要导入aysnc库。
```dart
import 'dart:async';
```

**Future<T>**
await必须在aysnc函数中使用，异步函数必须返回Future<T>来声明，其中T为返回数据的类型。
```dart
Future<void> runInFuture1() async {
  sleep(Duration(seconds: 2));
  print('runInFuture1');
}

Future<void> runInFuture2() async {
  sleep(Duration(seconds: 2));
  print('runInFuture2');
}

Future<void> run() async {
  await runInFuture1();
  print('running');
  await runInFuture2();
  print('run over');
}

print('start');
run();
print('over');
// output:
// start
// runInFuture1
// over
// running
// runInFuture2
// run over

// 不是await的回调方式，then返回的对象依然是Future类型的
run().then((value) => null);

// 异常捕获
try{
	await run();
}catch(e){}
// catchError返回的对象依然是Future类型的
run().then((value) => null).catchError((e) => null);

// Future.wait([])管理多个异步函数
Future.wait([runInFuture1(), runInFuture2()]);
```

**Stream**
Steam主要用于各种事件和文件流等，比如HTML的dom事件。
```dart
Future<void> searchFiles(searchPath) async {
  if (await FileSystemEntity.isDirectory(searchPath)) {
    final startingDir = Directory(searchPath);
	   // 使用await for遍历目录下所有文件
    await for (final entity in startingDir.list()) {
      if (entity is File) {
        print(entity);
      }
    }
  }
}
// 文件读取
Future<void> readFileAwaitFor() async {
	var config = File('config.txt');
	Stream<List<int>> inputStream = config.openRead();
	// 对流数据进行转换，类似pipe功能
	var lines = inputStream
      .transform(utf8.decoder)
      .transform(const LineSplitter());
	// 流异常捕获
 	try {
    await for (final line in lines) {
      print('Got ${line.length} characters from stream');
    }
    print('file is now closed');
 	} catch (e) {
    print(e);
 	}
	// 使用回调模式
	lines.listen((String line){
		print('Got ${line.length} characters from stream');
	}, onDone:(){
		print('file is now closed');
	}, onError:(e){
		print(e);
	})
}	
```

#### 数学
提供一些常用的处理数学问题的能力。需要导入math库。
```dart
import 'dart:math';
```

**常量值**
```dart
print(e); // 2.718281828459045
print(pi); // 3.141592653589793
print(sqrt2); // 1.4142135623730951
```

**三角函数**
```dart
sin(pi / 2); // 1.0
cos(pi); // -1.0
```

**最大值和最小值**
```dart
max(1, 1000); // 1000
min(1, -1000); // -1000
```

**随机数**
```dart
var random = Random();
random.nextDouble(); // Between 0.0 and 1.0: [0, 1)
random.nextInt(10); // Between 0 and 9.
random.nextBool(); // true or false
```

#### 转化
转化库提供了JSON和UTF-8以及一些其他编码的转化。需要导入convert库。
```dart
import 'dart:convert';
```

**JSON**
```dart
var json = '''
  {
    "age":10, 
    "name": "Tommy"
  }
''';
jsonDecode(json); // {age: 10, name: Tommy}

var scores = [
	{'score': 40},
	{'score': 80}
];
jsonEncode(scores); // [{"score":40},{"score":80}]
```

**UTF-8**
```dart
List<int> utf8Bytes = [
  0xc3, 0x8e, 0xc3, 0xb1, 0xc5, 0xa3, 0xc3, 0xa9,
  0x72, 0xc3, 0xb1, 0xc3, 0xa5, 0xc5, 0xa3, 0xc3,
  0xae, 0xc3, 0xb6, 0xc3, 0xb1, 0xc3, 0xa5, 0xc4,
  0xbc, 0xc3, 0xae, 0xc5, 0xbe, 0xc3, 0xa5, 0xc5,
  0xa3, 0xc3, 0xae, 0xe1, 0xbb, 0x9d, 0xc3, 0xb1
];
utf8.decode(utf8Bytes); // Îñţérñåţîöñåļîžåţîờñ

List<int> encoded = utf8.encode('Îñţérñåţîöñåļîžåţîờñ');
```

#### IO
IO库提供了一系列API去处理文件、目录、进程、sockets、WebSockets和HTTP请求。IO库只能在非web的Flutter应用、命令行脚本和服务端应用。需要导入IO库。
```dart
import 'dart:io';
```

**文件和目录**
在IO库中，提供了一系列能力来处理文件和目录的读取和写入。
```dart
var config = File('config.txt');

// 读取整个文件
var stringContents = await config.readAsString();
print('The file is ${stringContents.length} characters long.');

// 分行读取
var lines = await config.readAsLines();
print('The file is ${lines.length} lines long.');

// 按二进制读取
var contents = await config.readAsBytes();
print('The file is ${contents.length} bytes long.');

// 文件写入
var logFile = File('log.txt');
var sink = logFile.openWrite();
sink.write('FILE ACCESSED ${DateTime.now()}\n');
await sink.flush();
await sink.close();

```

**HTTP**
在IO库中提供了一些列类来访问HTTP资源和运行HTTP服务。
```dart
// 处理请求
void processRequest(HttpRequest request) {
  print('Got request for ${request.uri.path}');
  final response = request.response;
  if (request.uri.path == '/dart') {
    response
      ..headers.contentType = ContentType('text','plain',)
      ..write('Hello from the server');
  } else {
    response.statusCode = HttpStatus.notFound;
  }
  response.close();
}
// 监听请求
final requests = await HttpServer.bind('localhost', 8888);
await for (final request in requests) {
	processRequest(request);
}

// 发起请求
var url = Uri.parse('http://localhost:8888/dart');
var httpClient = HttpClient();
var request = await httpClient.getUrl(url);
var response = await request.close();
var data = await utf8.decoder.bind(response).toList();
print('Response ${response.statusCode}: $data');
httpClient.close();
```
