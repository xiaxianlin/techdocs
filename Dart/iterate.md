# 迭代
## 生成器
生成器是一种特殊的函数，分成**同步**和**异步**两种。
使用sync*修饰的函数就是同步生成器，同步生成器返回的对象类型只能是迭代类型，yield关键字表示等待，当迭代器调用moveNext的时候返回yeild后面的值。
```dart
Iterable<int> range(int n) sync*{
	int k = 0;
	while(k < n) yield k++;
}
```

## Iterator<E>
迭代器接口，为迭代器提供属性和行为规范，具体迭代器均需要实现该接口。

#### 属性
**current -> E** 
当前元素
```dart
var data = [1,2,3];
var listIterator = data.iterator; // 获取列表的迭代器
print(listIterator.current); // type 'Null' is not a subtype of type 'int' in type cast
```
初始的迭代器current为null，因为指针没有移动

#### 方法
**moveNext() -> bool** 
移到下一个元素
```dart
listIterator.moveNext(); // 移动到第一个元素
print(listIterator.current); // 1
```

#### 实现一个迭代器
```dart
class RangeIterator implements Iterator {
  var _n;
  var _current = 0;

  RangeIterator(this._n);

  get current => this._current;

  bool moveNext() {
    if (_current < _n) {
      _current++;
      return true;
    }
    return false;
  }
}
```


## Iterable<E>
**迭代**是对一组值或元素的集合按**顺序**访问。迭代需要通过**迭代器**去获取值或元素。
当一个迭代通过方法处理后返回一个新迭代，这个新迭代具有惰性，也就是这个新迭代只有在使用的时候才会生成，又名懒迭代。

#### 构造函数
**Iterable.empty()** 
构造一个空迭代
```dart
var it = Iterable.empty()
```

**Iterable.generate(int count, [E generator(index)?])** 
默认是一个执行count次的迭代，如果传入生成器，生成count个迭代的迭代
```dart
// 不传入生成器
var it = Iterable.generate(1);
for(final i in it){ // 相当于for(final i = 0; i < 0; i++)
	print(i);
}

// 传入生成器
var it = Iterable.generate(2, range); // 生成了range(0)和range(1)两个迭代的迭代器
for (final sit in it) { // 第一个迭代为()，第二个为(100)
	for (final i in sit) {
		print(i);
	} 
}
// 100
```

#### 属性
```dart
var it = Iterable.generate(2);
```

**first -> E** 
迭代的第一个元素
```dart
print(it.firt); // 0
```

**last -> E** 
迭代的最后一个元素
```dart
print(it.last); // 1
```

**single -> E** 
迭代仅有的一个元素，如果迭代长度超过1，则抛出异常
```dart
print(it.last); // Bad state: Too many elements
var singleIt = Iterable.generate(1);
print(it.last); // 0
```

**length -> int** 
迭代的长度
```dart
print(it.length); // 2
```

**isEmpty -> bool** 
迭代是否为空
```dart
print(it.isEmpty); // false
```

**isNotEmpty -> bool** 
迭代是否非空
```dart
print(it.isNotEmpty); // true
```

**iterator -> Iterator<E>** 
迭代的迭代器
```dart
var itor = it.iterator;
print(itor.current); // null
itor.moveNext();
print(itor.current); // 0
```

#### 方法
```dart
var it = Iterable.generate(10); // (0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
```

**any(bool test(E element)) -> bool** 
判断任一元素是否满足断言
```dart
it.any((element) => element < 5); // true
it.any((element) => element > 11); // false
```

**every(bool test(E element)) -> bool** 
判断所有元素是否满足断言
```dart
it.every((element) => element > 5); // false
it.every((element) => element < 11); // ture
```

**contains(Object? element) -> bool** 
迭代是否包含指定元素
```dart
it.contains(1); // true
it.contains(11); // false
```

**elementAt(int index) -> E** 
获取指定位置的元素
```dart
["a", "b", "c"].elementAt(1); // b
```

**firstWhere(bool test(E element), {E orElse()?}) -> E** 
找到第一个满足断言的元素，如果都不满足并且有orElse则触发orElse
```dart
["a", "b", "c", "d"].firstWhere((element) => element != "c"); // a
["a", "b", "c", "d"].firstWhere((element) => element != "e", orElse: () => "e"); // e
```

**lastWhere(bool test(E element), {E orElse()?}) -> E** 
找到最后一个满足断言的元素，如果都不满足并且有orElse则触发orElse
```dart
["a", "b", "c", "d"].lastWhere((element) => element != "c"); // d
["a", "b", "c", "d"].lasttWhere((element) => element == "e", orElse: () => "e"); // e
```

**singleWhere(bool test(E element), {E orElse()?}) -> E** 
找到满足断言的唯一元素，如果找不到并且有orElse则触发orElse
```dart
[1, 2, 3].singleWhere((element) => element == 2); // 2
// 如果查询到多个满足断言的元素，则会抛出异常
[1, 2, 3, 2].singleWhere((element) => element == 2); // Bad state: Too many elements
[1, 2, 3].singleWhere((element) => element == 5, orElse: () => 4); // 4
```

**fold<T>(T initialValue, T combine(T previousValue, E element)) -> T** 
通过迭代地将集合的每个元素与现有值组合，将集合减少为单个值
```dart
[1, 2, 3].fold<int>(0, (previousValue, element) =>previousValue + element); // 6
```

**reduce(E combine(E value, E element)) -> E** 
通过使用提供的函数迭代组合集合的元素，将集合减少到单个值
```dart
[1, 2, 3].reduce((value, element) => value + element)); // 6
```
与fold不同的是reduce不能改变返回值的类型，也不能初始一个值。

**forEach(void action(E element)) -> void** 
遍历整个迭代，每个元素都触发一次action
```dart
[1, 2, 3].forEach((e) => print(e * 2));
// 2
// 4
// 6
```

**map<T>(T toElement(E e)) -> Iterable<T>** 
根据toElement关系映射一个新的迭代
```dart
[1, 2, 3].map((e)=> "p$e"); // (p1, p2, p3)
```

**join([String separator = “”]) -> String** 
使用指定的分隔符连接迭代的每个元素，可不指定分隔符
```dart
[1, 2, 3].join(","); // "1,2,3"
```

**cast<R>() -> Iterable<R>** 
将元素的类型全部提升为类型R
```dart
class Anmiate {
  var name = "";
  Anmiate(this.name);
}
var list = [Anmiate("1"), Anmiate("2")] // 列表自带迭代特征
var objList = list.cast<Object>(); // 把所有元素提升为Object类型
for(final i in ObjList){
	print(i.name); // The getter 'name' isn't defined for the type 'Object'.
}
```

**expand<T>(Iterable<T> toElement(E element)) -> Iterable<T>** 
展开当前迭代的元素，并加入任意多元素或不加入任何元素
```dart
[["a", "b"],["c", "d"]].expand((e) => e); // (a, b, c, d)
```

**followedBy(Iterable<E> other) -> Iterable<E>** 
返回当前迭代和其他迭代合并的懒迭代
```dart
[1, 2].followedBy([3, 4]) // (1, 2, 3, 4)
```

**skip(int count) -> Iterable<E>** 
返回跳过前count个元素的迭代
```dart
[1, 2, 3, 4].skip(2); // (3, 4);
```

**skipWhile(bool test(E element)) -> Iterable<E>** 
返回首次不满足断言之后的所有元素的迭代
```dart
[1, 2, 3, 4].skipWhile((element) => element < 3); // (3, 4)
// 第一次断言：1 == 2，结果为false，跳过结束，因此全部返回
[1, 2, 3, 4].skipWhile((element) => element == 2); // (1, 2, 3, 4)
```

**take(int count) -> Iterable<E>** 
返回迭代中前count个元素构成的懒迭代
```dart
[1, 2, 3, 4].take(2); // (1, 2);
```

**takeWhile(bool test(E element)) -> Iterable<E>** 
返回首次不满足断言之前的所有元素的迭代
```dart
[1, 2, 3, 4].takeWhile((element) => element < 3); // (1, 2)
// 第一次断言：1 == 3，结果为false，获取结束，返回()
[1, 2, 3, 4].takeWhile((element) => element == 3); // ()
```

**where(bool test(E element)) -> Iterable<E>** 
返回所有符合断言的元素的懒迭代
```dart
[1, 2, 3, 4].where((element) => element % 2 == 0); // (2, 4)
```

**whereType<T>() -> Iterable<T>** 
返回所有符合类型的元素的懒迭代
```dart
[1, "2", "3", 4].whereType<String>(); // ("2", "3")
```

**toList({bool growable = true}) -> List<E>** 
将迭代转换成列表，并可以指定是否可以成长
```dart
[1, 2, 3, 4].take(2).toList(); // [1, 2]
```

**toSet() -> Set<E>** 
将迭代转换成无序列表
```dart
[1, 2, 3, 4].take(2).toSet(); // {1, 2}
```

#### 实现
通过继承IterableBase为对象添加迭代性质
```dart
class Range extends IterableBase<int> {
  @override
  final Iterator<Process> iterator = RangeIterator(10);
}

for(final i in Range()){
	print(i);
}
```

#### forin
具备迭代性质的对象都可以用forin来进行遍历，这是一种语法糖。
```dart
var data = [1, 2, 3, 4];
for(final i in data){
	print(i * 2);
}
// 2
// 4
// 6
// 8
```


## BidirectionalIterator
一个允许后移的迭代器接口，继承自Iterator。有RuneIterator继承。

#### 实现
```dart
class CursorIterator implements BidirectionalIterator {
  var _i = 0;
  List _data;

  CursorIterator(this._data);

  @override
  get current => _data[_i];

  @override
  bool moveNext() {
    _i++;
    return _i < _data.length;
  }

  @override
  bool movePrevious() {
    _i--;
    return _i < _data.length;
  }
}
```

#### 方法
**movePrevious() -> bool**
后移一个位置。
```dart
var it = CursorIterator([1, 2, 3]);
print(it.current); // 1
it.moveNext();
print(it.current); // 2
it.movePrevious();
print(it.current); // 1
```

## RuneIterator
一个读取字符串runes(Unicode编码)的迭代器，继承自BidirectionalIterator。

#### 构造函数
**RuneIterator(String string)**
构建一个从头开始的迭代器。
```dart
var rit = RuneIterator("String");
```

**RuneIterator\.at(String string, int index)**
构建一个从位置index开始的迭代器。
```dart
var rit = RuneIterator.at("String", 2);
```

#### 属性
```dart
var rit = RuneIterator.at("String", 2);
rit.moveNext();
```

**currentAsString → String**
获取当前字符。
```dart
rit.currentAsString; // 'r'
```

**currentSize → int**
包含当前符文的代码单元数。
```dart
rit.currentSize; // 1
```

**rawIndex ↔ int**
获取/设置迭代器的开始位置。
```dart
print(rit.rawIndex); // 2
rit.rawIndex = 4;
print(rit.currentAsString); // 'n'
```

**string → String**
正在被迭代的字符串。
```dart
rit.string; // 'String'
```

#### 方法
**reset([int rawIndex = 0]) -> void**
重置开始位置。
```dart
rit.reset(4);
rit.moveNext();
print(rit.currentAsString); // 'n'
```

## Runes
字符串的runes迭代类，继承自Iterable，拥有已经实现好了的RuneIterator实例。该类实例挂载在字符串实例的runes属性上。

#### 构造函数
**Runes(String string)**
创建指定字符串的Runes实例。
```dart
Runes('String');
```

#### 实现
```dart
var runes = Runes('String');
print(runes.first); // 83
print(runes.last); // 103
for (var r in runes) {
	print(r);
}
// 83
// 116
// 114
// 105
// 110
// 103
```
