# 对象
除了null以为所有对象的基础类。所有非Null类都是Object的子类。

#### 构造函数
**Object()** 
构造一个纯对象，啥也没有的那种
```dart
var obj = Object();
```

#### 属性
**hashCode -> int** 
计算对象的hashcode
```dart
obj.hashCode; // 347791204
```

**runtimeType -> Type** 
对象运行时的类型
```dart
obj.runtimeType; // Object
```

#### 方法
**noSuchMethod(Invocation invocation) -> dynamic** 
当一个不存在的方法或属性被访问时触发
```dart
class MockList<T> implements List<T> {
	// 重写noSuchMethod来处理错误访问
  noSuchMethod(Invocation invocation) {
    log(invocation);
    super.noSuchMethod(invocation); // Will throw.
  }
}

MockList().add(42); // 由于add没有被实现，调用add会触发调用noSuchMethod
```
主要是由系统调度，可以通过重写来处理。

**toString() -> String** 
对象的字符串表示。
```dart
obj.toString(); // Instance of 'Object'
```

如果对象遇到字符串操作时候会自动调用toString()方法。toString一般需要被重写。
```dart
class Foo {
  String toString() {
    return 'this is a Foo class.';
  }
}
print(Foo()); // 'this is a Foo class.'
```

#### 静态方法（需要在2.14及以上的版本才有）
**hash(Object? o1, Object? o2, [Object? o3 = sentinelValue, ..., Object? o20 = sentinelValue]) -> int** 
计算2个对象及以上的哈希值
```dart
Object.hash(Object(), Object()); // 358310331
```

**hashAll(Iterable<Object?> objects) -> int** 
计算一组有序对象的哈希值
```dart
Object.hashAll([Object(), Object()]); // 407153680
```

**hashAllUnordered(Iterable<Object?> objects) -> int** 
计算一组有序对象的哈希值
```dart
Object.hashAllUnordered({Object(), Object()}); // 181481652
```
