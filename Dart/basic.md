# 基础
### 操作符
- 一元运算符
	- 前置：expr++、expr—、()、[]、?[]、.、?.
	- 后置：-expr、!expr、~expr、++expr、--expr、await expr
- 计算：+、-、*、/、%、~/(除法取整)
- 位移：<<、>>、>>>
- 按位：&、^、|
- 判断：>=、>、<=、<、==、!=、as、is、is!
- 逻辑：&&、||
- 判断null：??
- 三元判断：expr1 ? expr2 : expr3
- cascade：..、?..
- 赋值：=、*=、%=、>>>=、^=、+=、/=、<<=、&=、|=、-=、~/=、>>=

### 基础类型
使用var声明的变量会自动推断变量类型。

**String**：字符串
```dart
var a = "aaa";
String b = "bbb";
String c = 'ccc'; // 单引号也可以
```

**Number**：数值
```dart
var a = 1;
var b = 1.1;
int c = 1;
double d = 1.2;
// 使用num声明的变量可以在int和double之间互相赋值
num e = 1; // 比如e=1.2，也不会保持
num f = 1.2;

[x] Number g = 12; // 没有这种声明方法
```

**Boolean**：布尔
```dart
var a = true;
bool b = false;
```

**List**：列表
```dart
var list = [1,2,3]; // 根据初始数据推断具体泛型为List<int>
var stringList = <String>[]; // 带类型的字面量
[x] list.add("a"); // 会抛出异常

List list1 = [4,5,6, "a"]; // 不指定泛型，初始值会混合数据则默认泛型为List<Object>
List<int> list2 = [7,8,9]; // 指定泛型
```

**Set**：无序集合
```dart
var ids = {1,2,3,4}; // 类似列表，推断类型为Set<int>，无法添加int以外的类型
var ages = <int>{}; // 明确指定类型约束
Set names = {1, "Tom"}; // 使用类型关键字声明，不带类型约束
Set<String> words = {"this", "is"}; // 使用类型关键字声明，带类型约束
```

**Map**：映射
```dart
// 也可以使用Map<String, String> giftsh或者Map gifts来声明
var gifts = {
  // Key:    Value
  'first': 'partridge',
  'second': 'turtledoves'
};

var test = Map<Object, String>();
test[gifts] = "gifts";
```

**类型别名**
```dart
typedef IntList = List<int>;
IntList il = [1, 2, 3];

typedef ListMapper<X> = Map<X, List<X>>;
ListMapper<String> m2 = {};

typedef Compare<T> = int Function(T a, T b)
```

### 函数
**普通函数**
```dart
bool isEmpty(List data){
	return data.length == 0;
}
// 无返回函数使用void声明，其中void可以省略
void log(){}
log1(){}
```

**函数参数**
```dart
// 参数可以不声明类型
log(info){ print(info); }

// 参数声明类型
log(String info){ print(info); }

// 可为空参数
enableFlags(bool? bold, bool? hidden){}

// 可选参数
void log(String info, [String? date, int? id]) {}
void log(String info, [String? date, int? id = 123]) {} // 可选参数带默认值
log("test");
log("test", "date");
log("test", "date", 123);

// 命名参数
enableFlags({bool? bold, bool? hidden}){} // 参数可为空
enableFlags({bool? bold, bool hidden = true}){} // 带默认值
enableFlags({required bool bold, bool hidden = true}){} // bold为必传参数
enableFlags(bold: true, hidden: false);
```

**箭头函数**
```dart
// 箭头函数没有函数体，只能直接返回结果
var say = (String name) => 'hello, $name';
```

**匿名函数**
```dart
const list = ['apples', 'bananas', 'oranges'];
list.forEach((item) { print('${list.indexOf(item)}: $item'); });
```

**主函数**：程序入口
```dart
void main(){}
```

**其他**
- 函数可以作为一等对象
- 函数可以作为参数传入
- 函数可以作为结果返回

### 类
**获取对象的类**
```dart
var test = Test();
Type t = test.runtimeType;
```

**构造函数**
```dart
const double xOrigin = 0;
const double yOrigin = 0;

class Point{
	double? x,y;
	
	Point(); // 普通构造函数
	Point(this.x, this.y); // 自动赋值构造函数，且无处理
	Point(this.x, this.y){} // 自动赋值构造函数，且有处理
	Point(this.x, this.y, int z){} // 自动赋值构造函数并带额外参数，且有处理
	Point(Map data) : super.fromJson(data); // 普通构造函数调用父级命名构造函数

	const Point(this.x, this.y); // 常量构造函数，只能赋值给final修饰的属性

	Point.init(); // 普通命名构造函数
	Point.origin() : x = xOrigin,y = yOrigin; // 带赋值的命名构造函数
	Point.origin() : x = xOrigin,y = yOrigin {} // 带赋值的命名构造函数，带处理
	Point.from(double x, double y) : this(x, y); // 命名构造函数调用普通构造函数
	Point.from(double x, double y) : this(x, y){} // 命名构造函数调用普通构造函数，待处理
	// 如果一个类有父类，那么子类的命名构造函数必须跟父类，无法新增
	Point.fromJSON(Map data) : super.fromJSON(data); // 命名构造函数调用父级命名构造函数
	Point.fromJSON(Map data) : super.fromJSON(data){} // 命名构造函数调用父级命名构造函数，带处理
}

```

**运算符重载**
```dart
class Point {
  final double x, y;

  const Point(this.x, this.y);
	
 	Point operator +(Point p) => Point(x + p.x, y + p.y);
	Point operator -(Point p) => Point(x - p.x, y - p.y);
}
var p1 = Point(1, 1);
var p2 = Point(2, 2);
p1 + p2; // Point{x:3, y:3}
p2 - p1; // Point{x:1, y:1}
```

**对象为函数**
```dart
class Axis {
  String call(int x, int y) => "x: $x, y: $y";
}
var axis = Axis();
var pos = axis(12, 23);
print(pos); // x: 12, y: 23
```

### 异常
**抛出异常**
```dart
throw FormatException('Expected at least 1 section');
throw 'Out of llamas!';
```

**捕获异常**
```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException { // 捕获指定异常
  buyMoreLlamas();
} on Exception catch (e) { // 捕获指定异常并获得指定信息
  print('Unknown exception: $e');
} catch (e, s) { // 捕获其他所有未指定的异常，并获取异常信息，s为异常栈路径信息
  print('Something really unknown: $e');
	rethrow; // 重新抛出异常
}
```

### 异步
主要提供Future和Stream两种异常类型

**异步函数**
```dart
// 使用async声明为异步函数，返回Future对象，泛型为返回数据的类型
Future<void> checkVersion() async {
	await doSomethings(); // await只能在async函数里面使用
}
```

**异步流**
```dart
Future<void> main() async {
	// 主要使用await for来等待异步流
	await for (final request in requestServer) {
  	handleRequest(request);
	}
}
```

### 生成器
同步生成器返回迭代器（terable），异步生成器返回流对象（Stream）
```dart
// 同步生成器
Iterable<int> naturalsTo(int n) sync* {
  int k = 0;
  while (k < n) yield k++;
}
// 异步生成器
Stream<int> asynchronousNaturalsTo(int n) async* {
  int k = 0;
  while (k < n) yield k++;
}
```

### 方法扩展
```dart
extension <extension name> on <type> {
  (<member definition>)*
}

extension NumberParsing on String {
  int parseInt() {
    return int.parse(this);
  }
  double parseDouble() {
    return double.parse(this);
  }
}

"42".parseInt() // 42

```

### 集合的内解析
```dart
// 在集合内使用if判断
["bb", if (true) "cc", "dd"] // ["bb", "cc", "dd"]
{"a", if(false) "b"} // {a}
{"a": 1, if(true) "b": 2} // {a: 1, b: 2}

// 在集合内使用for循环
var data = [1,2,3];
[for(var i in data) i] // [1,2,3]
{for(var i in data) i} // {1,2,3}
{for (var i in data) "$i": i} {1: 1, 2: 2, 3: 3}

// for和if可以嵌套使用
```

### 库
**导入重命名**：相当显示指定命名空间
```dart
import 'package:lib2/lib2.dart' as lib2;
lib2.<xxxx>
```

**模块显示与隐藏**
```dart
// 只导入foo
import 'package:lib1/lib1.dart' show foo;

// 不导入foo
import 'package:lib2/lib2.dart' hide foo;
```

**延迟导入**
```dart
import 'package:greetings/hello.dart' deferred as hello;

Future<void> greet() async {
  await hello.loadLibrary(); // 需要等待导入成功
  hello.printGreeting();
}
```
