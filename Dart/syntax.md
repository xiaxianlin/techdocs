# 语法
### 声明
直接声明变量使用var，参数变量需要用数据类型来描述
```dart
// 变量声明
var name = "Tom"; // 普通变量声明

int age = 12; // 带类型变量声明
int? id; // 可为null变量声明，如果不带?，变量必须赋值，除非是late延迟赋值
late String text; // 延迟变量，如果没赋值就被使用的话，会抛出LateInitializationError异常

// 只读变量
final a = 1; // 不带类型的只读变量声明
final int b = 2; // 带类型的只读变量声明
final s = Set(); // 对象类型的只读变量声明，但是对象内的属性可以被修改

// 常量，常量只能用来修复普通数据类型，也就是直接值
const a = 1; // 不带类型的常量声明
const int b = 2; // 带类型的常量声明
[x] const a = Set(); // 无法通过编译，对象类型没法进行常量声明的

var foo = const[]; // 给普通变量赋值一个常量
[x] foo.add(1); // 会抛出异常Unsupported operation
foo = [1]; // 重新赋值是允许的，毕竟只是个普通变量

final bar = const []; // 给只读变量进行常量赋值，这意味值完全不能变化
const bar = []; // 相当于var bar = const [];

// 函数声明
int fibonacci(int n){
	return 0;
}
```

### 条件控制
**判断**
```dart
// 判断
if(true){ /* todo */ }else if(false){ /* todo */ }else{ /* todo */ }
```

**循环**
```dart
for(int i = 0; i < 10; i++){ /* todo */ }
for(final object in objects){ /* todo */ }
while(true){ /* todo */ }
```

### 普通类
```dart
// 定义类，类属性和方法均需要具体类型来描述
class Base{
	static int uuid = 0; // 静态属性
	String _flag = 0; // 私有属性，用_来定义，在当前文件内实例化的对象是可以直接访问私有属性，被导出后，私有属性无法访问
	int id = 1; // 公共属性，不可为null，直接初始化
	String name; // 未初始的属性，必须用构造函数初始
	int？_age; // 可为null的属性，sdk在2.12.0以上才支持
	
	// getter & setter
	int get nameLen => name.length; // 特殊的箭头函数吧？
	int? get age{ return _age; }
	set age(int? value){ _age = value; } // setter的参数的类型必须给getter的类型一直

	// 构造函数，只能有一个，无法重载
	Base(this.name); // 构造的时候直接赋值给name
	Base(this.name){} // 赋值后继续处理其他业务
	Base(this.name, int age){ this._age = age; } // 第一个参数会自动赋值给name属性
	Base(this.name, int age, int? id){} // 第三个参数表示可以传入null，但是在使用构造函数的时候必须传入3个参数

	// 命名构造函数，大概是用来模拟重载的吧，返回实例对象
	Base.instance(String name) : this(name, 1, null);
	Base.instance2(String name, int age) : this(name, age, null);
	Base.instance3(String name, int age, int id) : this(name, age, id);

	// 方法
	static void go(){} // 静态方法
	void describe(){} // 不带参数无返回的公共方法
	int counter(){ return 0; } // 不带参数带返回的公共方法
	int add(int a, int b){ return a + b; } // 带参数带返回的公共方法
	void _test(){} // 私有方法
}

// 实例化
var b = Base("name");
var b1 = Base.instance("name");
// 属性赋值
b.age = 123;
// 调用属性
print(b.age);
// 调用方法
b.add(1, 2);
```

### 抽象类
抽象类使用abstract修饰，具有普通类所有能力，并且可以定义抽象方法，即只声明方法不实现。普通类继承抽象类后必须实现抽象方法
```dart
abstract class AbstractBase{
	String context;
	
	AbstractBase(this.context); // 抽象类可以定义构造函数，但是不能被实例化

	void log(); // 抽象方法
}

[x] AbstractBase(""); // 编译不会通过，会报异常
```

### 继承
普通类可以继承普通类、抽象类，抽象类也可以继承普通类和抽象类，但是只能继承一个类，继承后可以使用所有父类的属性和方法,
```dart
abstract class A{
	void log(); // 抽象方法
}
class B{
	String name() => "class B";
}

abstract class C extends A{}
abstract class D extends B{}

class E extends A{
	@override
  void log() {} // 必须实现A的抽象方法
} 
class F extends B{
	void log(){ print(super.name()); } // super优先调用直接父级，如果直接上级没有就继续向上寻找，直到找到即停止
}
```

### 接口与实现
接口也是一种普通类，只有当用implements关键字进行实现，该类会变成接口，实现类必须重写接口全部属性和方法，可实现多个接口
```dart
class Base{
	int id = 1;
	String name;
	
	Base(this.name);

	void greet(){ print("hello ${this.name}"); }
}
// 将Base类当作接口来实现
class Test implements Base {
  @override
  int id = 1;

  @override
  String name;

  Test(this.name);

  @override
  void greet() { print("hello ${this.name}"); }
}
```

### Mixin与混合
Mixin是一种特殊的类，使用mixin关键字声明，不能实例化，只能用with混合进其他普通类，普通类可以混合多个mixin
```dart
mixin Person{
	int age = 1;
	int doubleAge(){ return age * 2; }
}

mixin Fly{
	String name = "person";
	void fly() => print("fly: ${this.name}")
}

class Test with Person{}

var c = Test();
print(c.doubleAge()); // 2
c.fly(); // fly: person
```

### 异常
```dart
if(num == 0){
	throw StateError("No num."); // 通过throw关键字抛出异常
}

// 使用try catch捕获异常
try {
  for (final object in flybyObjects) {
    var description = await File('$object.txt').readAsString();
    print(description);
  }
} on IOException catch (e) { // on关键字判断异常类型
  print('Could not describe object: $e');
} finally { // 捕获异常后必然的处理
  flybyObjects.clear();
}
```

### 导入与导出
**导入**
```dart
// 核心库导入
import 'dart:math';
// 三方库导入
import 'package:test/test.dart';
// 文件导入
import 'paths/file.dart'
```

**导出**
在文件中所有声明的变量、函数以及类等默认被导出，如果不想被导出需要在名称前加_
```dart
// a.dart
var aName = "tom";
int count(){
	return 0;
}
class Foo(){}

// b.dart
import 'a.dart';
var bName = aName;
var num = count();
var f = Foo();
```

### 特殊操作符
**$**与**${}**：字符串中的变量引用
```dart
var a = 1;
var b = {"c": 2};

print("$a count"); // 普通变量使用
print("${b['c']} count"); // 对象属性使用
```

**?**：可为null声明，与部分操作符合作时可以做null判断
```dart
int? a;
void test(int? b){};
```

**??**：类似于熔断选择，需找到第一个非null值为止
```dart
print(1 ?? 3); // 1
print(null ?? 3); // 3
print(null ?? null ?? 5); // 5
```

**??=**：用来对可为null的变量进行默认值设置
```dart
void log(int? a){
	a ??= 5;
	print(a);
}

log(null); // 5
log(3); // 3
```

**..**：级联，可以用来声明并连续赋值
```dart
var paint = Paint()
  ?..color = Colors.black
  ..strokeCap = StrokeCap.round
  ..strokeWidth = 5.0;

```

**…**：对象属性展开
```dart
// 展开列表
var list = [1,2,3];
var list2 =[...list,4,5,6];
// 展开非空判断，需要在展开后加?
var nullList;
var list3 = [1,2,3, ...?nullList];
```
