# 集合
集合管理一系列数据，主要分为有序列表（List）、无序列表（Set）、键值映射（Map）。。

## List
有序列表分为可变长度和固定长度两种。在构造列表的时候指定growable为true，则列表长度是可变大。有序列表实现了Iterable。可以直接声明也可以构造函数构建对象。
```dart
var list = [];
var stringList = <String>[];
// 索引访问
list[0];
```

有序列表是通用性列表，Dart还提供了更多实现有序列表的功能性列表，比如:
DomRectList,DomStringList,FileList,Float32List,Float32x4List,Float64List,Float64x2List,HtmlCollection,ImmutableListMixin,Int8List,Int16List,Int32List,Int32x4List,Int64List,LengthList,ListMixin,MimeTypeArray,NodeList,NumberList,PluginArray,SourceBufferList,SpeechGrammarList,StringList,TextTrackCueList,TextTrackList,TouchList,TransformList,Uint8ClampedList,Uint8List,Uint16List,Uint32List,Uint64List,UnmodifiableFloat32ListView,UnmodifiableFloat32x4ListView,UnmodifiableFloat64ListView,UnmodifiableFloat64x2ListView,UnmodifiableInt8ListView,UnmodifiableInt16ListView,UnmodifiableInt32ListView,UnmodifiableInt32x4ListView,UnmodifiableInt64ListView,UnmodifiableListView,UnmodifiableUint8ClampedListView,UnmodifiableUint8ListView,UnmodifiableUint16ListView,UnmodifiableUint32ListView,UnmodifiableUint64ListView

#### 构造函数

**List.empty({bool growable = false})** 
创建一个固定长度为0的列表，并可以指定是否可变长度。
```dart
var data = List.empty();
data.add(1); // Unsupported operation: Cannot add to a fixed-length list
```

**List.filled(int length, T fill, {bool growable = false})** 
创建一个固定长度的列表，并默认填充数据
```dart
var data = List.filled(3, 2);
// [2, 2, 2]
```

**List.from(Iterable elememts,  {bool growable = true})**
把一个迭代转换成列表。并可以指定是否可变长度。
```dart
List.from([0, 1, 2, 3, 4]); // [0, 1, 2, 3, 4]
```

**List.generate(int length, E generator(int index),  {bool growable = true})**
生成一个指定长度且有数据的列表。并可以指定是否可变长度。
```dart
List<int>.generate(3, (int index) => index * index); // [0, 1, 4]
```

**List.of(Iterable<E> elememts,  {bool growable = true})**
把迭代转换成一个列表。并可以指定是否可变长度。如果List指定了类型，迭代的元素必须是指定类型。
```dart
List.of([0, 1, 2, 3, 4]); // [0, 1, 2, 3, 4]
List<int>.of([0, 1, 2, 3, '4']); // The element type 'String' can't be assigned to the list type 'int'.
```

**List.unmodifiable(Iterable elememts)**
把迭代转换成一个不可变列表。
```dart
var list = List.unmodifiable([0, 1, 2, 3, 4]);
list[1] = 10; // Unsupported operation: Cannot modify an unmodifiable list
```

#### 属性
```dart
var list = [1, 2, 3];
```

**first -> E** 
继承自迭代，读取或写入列表的第一个元素
```dart
list.first = 4; // [4, 2, 3]
```

**last -> E** 
继承自迭代，读取或写入列表的最后个元素
```dart
list.last = 4; // [1, 2, 4]
```

**reversed -> Iterable<E>**
反转集合，并返回一个新迭代。
```dart
list.reversed; // (3, 2, 1)
```

#### 方法
```dart
var list = [1, 2, 3];
```

**add(E value) → void**
添加一个元素。
```dart
list.add(4); // [1, 2, 3, 4]
```

**addAll(Iterable<E> iterable) → void**
添加一个迭代进入列表。
```dart
list.addAll([4, 5]); // [1, 2, 3, 4, 5]
```

**asMap() → Map<int, E>**
将列表转换成不可修改的map，索引为key，值为value。
```dart
list.asMap(); // {0: 1, 1: 2, 2: 3};
```

**clear() → void**
移除列表内所有元素。
```dart
list.clear(); // []
```

**fillRange(int start, int end, [E? fillValue]) → void**
范围内填充数据。可指定填充值。0 ≤ start < end ≤ list.length。
```dart
list.fillRange(0, 1, -1); // [-1, 2, 3]
```

**getRange(int start, int end) → Iterable<E>**
根据列表范围生成一个迭代。
```dart
list.getRange(0, 2); // (1, 2)
```

**indexOf(E element, [int start = 0]) → int**
获取元素的第一次匹配的索引，可设置检索起始位置。没找到则返回-1。
```dart
list.indexOf(2); // 1
list.indexOf(2, 3); // -1
```

**indexWhere(bool test(E element), [int start = 0]) → int**
根据断言获取元素的第一次匹配的索引，可设置检索起始位置。没找到则返回-1。
```dart
list.indexWhere((item) => item == 2); // 1
```

**insert(int index, E element) → void**
在指定位置插入元素。
```dart
list.insert(1, 10); // [1, 10, 2, 3]
```

**insertAll(int index, Iterable<E> iterable) → void**
在指定位置插入一个迭代的全部元素。
```dart
list.insert(1, [10, 11]); // [1, 10, 11, 2, 3]
```

**lastIndexOf(E element, [int? start]) → int**
获取元素的最后一次匹配的索引，可设置检索起始位置。没找到则返回-1。
```dart
var data = [1, 2, 3, 2, 4];
data.lastIndexOf(2); // 4
```

**lastIndexWhere(bool test(E element), [int? start]) → int**
根据断言获取元素的最后一次匹配的索引，可设置检索起始位置。没找到则返回-1。
```dart
var data = [1, 2, 3, 2, 4];
data.lastIndexWhere((item) => item == 2); // 4
```

**remove(Object? value) → bool**
移除第一个被匹配到的指定元素。如果元素存在返回true，不存在返回false。
```dart
list.remove(2); // true
list.remove(4); // false
```

**removeAt(int index) → E**
移除指定位置的元素，并返回该元素。
```dart
list.removeAt(1); // 2
```

**removeLast() → E**
移除最后一个元素，并返回该元素。
```dart
list.removeLast(); // 3
```

**removeRange(int start, int end) → void**
移除指定范围内的元素。
```dart
list.removeRange(0, 2); // [3]
```

**removeWhere(bool test(E element)) → void**
移除符合断言的元素。
```dart
list.removeWhere((item) => item % 2 == 1); // [2]
```

**replaceRange(int start, int end, Iterable<E> replacements) → void**
将指定范围的元素替换成指定迭代内的元素。
```dart
list.replaceRange(1, 2, [10, 11]); // [1, 10, 11, 3]
```

**retainWhere(bool test(E element)) → void**
保留符合断言的元素。
```dart
list.retainWhere((item) => item > 1); // [2, 3]
```

**setAll(int index, Iterable<E> iterable) → void**
从指定位置开始将迭代内的元素重新写入列表。iterable.length ≤ list.length - index。
```dart
list.setAll(1, [11, 12]); // [1, 11, 12]
```

**setRange(int start, int end, Iterable<E> iterable, [int skipCount = 0]) → void**
将迭代的元素重写指定范围内的元素。并可以指定忽略迭代的数量。
```dart
var list1 = [1, 2, 3, 4];
var list2 = [5, 6, 7, 8, 9];
list1.setRange(1, 3, list2); // [1, 5, 6, 4]
list1.setRange(1, 3, list2, 3); // [1, 8, 9, 4]
```

**shuffle([Random? random]) → void**
随机打乱集合。可以指定生成随机的对象。
```dart
list.shuffle(); // [2, 1, 3]
```

**sort([int compare(E a, E b)?]) → void**
列表排序。可指定对比函数。
```dart
var list1 = [2, 4, 1, 5, 3, 6];
list1.sort(); // [1, 2, 3, 4, 5, 6]
list1.sort((a, b) => b - a); // [6, 5, 4, 3, 2, 1]
```

**sublist(int start, [int? end]) → List<E>**
返回一个指定区间的子列表。如果没有传入end，则默认到最后。
```dart
list.sublist(0, 2); // [1, 2]
list.sublist(2); // [3]
```

#### 静态方法
**copyRange<T>(List<T> target, int at, List<T> source, [int? start, int? end]) → void**
把一个列表指定位置开始的数据复杂另一个指定区间的列表里面。
```dart
var list1 = [1, 2, 3];
var list2 = [4, 5, 6, 7, 8, 9];
List.copyRange(list2, 1, list1); // [4, 1, 2, 3, 8, 9]
List.copyRange(list2, 3, list1, 1); // [4, 5, 6, 2, 3, 9]
```

**writeIterable<T>(List<T> target, int at, Iterable<T> source) → void**
把一个迭代的元素写入指定开始位置的列表。
```dart
var source = {1, 2, 3};
var target = [4, 5, 6, 7, 8, 9];
List.writeIterable(target, 0, source); // [1, 2, 3, 7, 8, 9]
```

## Set
无序列表，每个元素都是唯一不能重复，没有固定的存储顺序，不能使用索引访问。实现了Iterable。可以通过字面量声明也可以使用构造函数。
```dart
var data = <int>{1, 2, 3};
var set = Set();
```

主要的实现类有：
CssClassSet,HashSet,LinkedHashSet,SetMixin,UnmodifiableSetView
Set的默认实现类是LinkedHashSet。

#### 构造函数
**Set()**
创建一个空的无序列表。
```dart
Set(); // {}
```

**Set.from(Iterable elements)**
创建一个包含所有迭代元素的无序列表。
```dart
Set.from([1, 2, 3]); // {1, 2, 3}
```

**Set.identity()**
创建一个具有恒等性质的空无序列表。大概参考数学上的恒等函数把...
```dart
Set.identity(); // {}
```

**Set.of(Iterable<E> elements)**
从迭代中创建一个无序列表。如果Set指定了类型，迭代的元素必须是指定类型。
```dart
Set.of([1, 2, 3]); // {1, 2, 3}
```

**Set.unmodifiable(Iterable<E> elements)**
从迭代中创建一个不可修改的无序列表。
```dart
var set = Set.unmodifiable([1, 2, 3]);
set.add(4); // Unsupported operation: Cannot change an unmodifiable set
```

#### 方法
```dart
var set = {1, 2, 3};
```

**add(E value) → bool**
添加一个元素。
```dart
set.add(4); // {1, 2, 3, 4}
```

**addAll(Iterable<E> elements) → void**
批量添加元素。
```dart
set.addAll([4, 5, 6]); // {1, 2, 3, 4, 5, 6}
```

**clear() → void**
移除全部元素。
```dart
set.clear(); // {}
```

**containsAll(Iterable<Object?> other) → bool**
判断是否全部包含迭代内的元素。
```dart
set.containsAll([1, 2, 3]); // true
```

**difference(Set<Object?> other) → Set<E>**
求异项。
```dart
set.difference({1, 2}); // { 3 }
```

**intersection(Set<Object?> other) → Set<E>**
求交集。
```dart
set.intersection({1, 2, 4}); // { 1, 2 }
```

**lookup(Object? object) → E?**
查找集合内和指定对象相等的对象，并返回。
```dart
set.lookup(2); // 2
```

**remove(Object? value) → bool**
移除指定对象。如果不存在，则返回false。
```dart
set.remove(2); // true
```

**removeAll(Iterable<Object?> elements) → void**
批量移除。
```dart
set.removeAll([1, 2]); // { 3 }
```

**removeWhere(bool test(E element)) → void**
移除复合断言的元素。
```dart
set.removeWhere((e)=> e % 2 == 1); // { 2 }
```

**retainAll(Iterable<Object?> elements) → void**
保留所有存在在指定迭代中的元素。
```dart
set.retainAll([1, 2, 6]); // {1, 2}
```

**retainWhere(bool test(E element)) → void**
保留所有符合断言的元素。
```dart
set.retainWhere((e) => e % 2 == 1); // {1, 3}
```

**union(Set<E> other) → Set<E>**
求并集。
```dart
set.union({4, 5, 3}); // {1, 2, 3, 4, 5}
```

## MapEntry
用来封装Map的键值信息的一个词条类。

#### 构造函数
**MapEntry(K key, V value)**
根据key/value构造一个map词条
```dart
var entry = MapEntry(1, "T");
```

#### 属性
**key -> K**
读取key。
```dart
entry.key; // 1
```

**value -> V**
读取value。
```dart
entry.value; // 'T'
```

## Map
一个由键值对结构存储的集合，键与值一一对应。属于一个顶级接口，默认实现类是LinkedHashMap。可以通过字面量或者构造函数构建。
```dart
var map = {1: "a", 2: "b", 3: "c"};
var map = Map();
// 使用key访问
map[1]; // 'a'
```

主要实现类有：
HashMap,HttpSession,LinkedHashMap,MapMixin,MapView,UnmodifiableMapBase,UnmodifiableMapView

#### 构造函数
**Map()**
创建一个空的LinkedHashMap实例。
```dart
Map(); // {}
```

**Map.from(Map other)**
创建一个与其他map相同的键和值的map。
```dart
Map.from({1: "a", 2: "b", 3: "c"}); // {1: a, 2: b, 3: c}
```

**Map.fromEntries(Iterable<MapEntry<K, V>> entries)**
创建一个新map，添加所有词条。
```dart
Map.fromEntries([MapEntry(1, 'a'), MapEntry(2, 'b')]); // {1: a, 2: b}
```

**Map.fromIterable(Iterable iterable, {K key(dynamic element)?, V value(dynamic element)?})**
创建一个新map，添加迭代中所有元素，默认key/value都为元素。也可以指定key/value的生成函数
```dart
Map.fromIterable(['a', 'b', 'c']); // {a: a, b: b, c: c}
Map<int, String>.fromIterable(['a', 'b', 'c'], key: (e) => e.codeUnitAt(0)); // {97: a, 98: b, 99: c}
```

**Map.fromIterables(Iterable<K> keys, Iterable<V> values)**
根据键迭代和值迭代创建一个新的map。
```dart
Map.fromIterables([1, 2, 3], ['a', 'b', 'c']); // {1: a, 2: b, 3: c}
```

**Map.identity()**
创建一个具有恒等性质的map。
```dart
Map.identity(); // {}
```

**Map.of(Map<K, V> other)**
创建一个与其他map相同的键和值的map。如果Map指定了类型，迭代的元素必须是指定类型。
```dart
Map.of({1: "a", 2: "b", 3: "c"}); // {1: a, 2: b, 3: c}
```

**Map.unmodifiable(Map other)**
创建一个与其他map相同的键和值的不可变map。
```dart
var map = Map.unmodifiable({1: "a", 2: "b", 3: "c"}); // {1: a, 2: b, 3: c}
map.addAll({4: 'd'}); // Unsupported operation: Cannot modify unmodifiable map
```

#### 属性
```dart
var map = {1: "a", 2: "b", 3: "c"};
```

**entries → Iterable<MapEntry<K, V>>**
获取map的全部词条。
```dart
map.entries; // (MapEntry(1: a), MapEntry(2: b), MapEntry(3: c))
```

**keys → Iterable<K>**
获取map的全部key。
```dart
map.keys; // (1, 2, 3)
```

**values → Iterable<V>**
获取map的全部value。
```dart
map.values; // (a, b, c)
```

#### 方法
```dart
var map = {1: "a", 2: "b", 3: "c"};
```

**addAll(Map<K, V> other) → void**
把其他的map的数据添加到当前map。
```dart
map.addAll({4: 'd'}); // {1: "a", 2: "b", 3: "c", 4: 'd'}
```

**addEntries(Iterable<MapEntry<K, V>> newEntries) → void**
把词条集合添加到当前map。
```dart
map.addEntries([MapEntry(4, 'd')]); // {1: "a", 2: "b", 3: "c", 4: 'd'}
```

**clear() → void**
清除所有数据。
```dart
map.clear(); // {}
```

**containsKey(Object? key) → bool**
判断是否包含指定key。
```dart
map.containsKey(2); // true
```

**containsValue(Object? value) → bool**
判断是否包含指定value。
```dart
map.containsValue('b'); // true
```

**forEach(void action(K key, V value)) → void**
遍历所有词条，并调用回调函数，传入key和value。
```dart
map.forEach((key, value) => print('key: $key, value: $value'));
// key: 1, value: a
// key: 2, value: b
// key: 3, value: c
```

**map<K2, V2>(MapEntry<K2, V2> convert(K key, V value)) → Map<K2, V2>**
遍历所有词条，并通过convert函数进行映射转换，生成新的map。
```dart
map.map((key, value) => MapEntry(value, key)); // {a: 1, b: 2, c: 3}
```

**putIfAbsent(K key, V ifAbsent()) → V**
查找一个词条，如果不存在该key，就插入一条。
```dart
map.putIfAbsent(2, () => 'd') // 'b'
map.putIfAbsent(4, () => 'd'); // 'd'
print(map); // {1: a, 2: b, 3: c, 4: d}
```

**remove(Object? key) → V?**
删除指定key的词条，并返回value。
```dart
map.remove(1); // 'a'
```

**removeWhere(bool test(K key, V value)) → void**
删除符合断言的词条
```dart
map.removeWhere((key, value) => key > 1); // {1: a}
```

**update(K key, V update(V value), {V ifAbsent()?}) → V**
更新一个词条，如果不存在该key，就插入一条。返回value
```dart
map.update(1, (v) => 'e'); // 'e'
map.update(4, (v) => v, ifAbsent: () => 'e'); // 'e'
print(map); // {1: a, 2: b, 3: c, 4: e}
```

**updateAll(V update(K key, V value)) → void**
批量更新词条。
```dart
map.updateAll((key, value)=> key.toString() + value); // {1: 1a, 2: 2b, 3: 3c}
```

## Queue
队列是一个可以在两端操作的集合。可以通过 forEach 或使用 Iterator 迭代队列的元素。实现了Iterable，有DoubleLinkedQueue，ListQueue两个实现类。

在使用Queue时需要到导入**dart:collection**
```dart
import 'dart:collection';

var queue = Queue();
```

#### 构造函数
**Queue()**
创建一个空队列。
```dart
var queue = Queue();
```

**Queue.from(Iterable Elements)**
创建一个队列，并将迭代中元素全部添加进去。
```dart
var queue = Queue.from([1, 2, 3, 4]);
```

Queue.of(Iterable<E> Elements)
创建一个队列，并将迭代中元素全部添加进去。如果Queue指定了类型，迭代的元素必须是指定类型。
```dart
var queue = Queue.of([1, 2, 3, 4]);
```

#### 方法
```dart
var queue = Queue.from([1, 2, 3, 4]);
```

**add(E value) → void**
添加一个元素。
```dart
queue.add(5); // {1, 2, 3, 4, 5}
```

**addAll(Iterable<E> iterable) → void**
批量添加元素。
```dart
queue.addAll([5, 6]); // {1, 2, 3, 4, 5, 6}
```

**addFirst(E value) → void**
添加元素到首部。
```dart
queue.addFirst(0); // {0, 1, 2, 3, 4, 5}
```

**addLast(E value) → void**
添加元素到尾部。
```dart
queue.addLast(5); // {1, 2, 3, 4, 5}
```

**clear() → void**
移除所有元素。
```dart
queue.clear(); // {}
```

**remove(Object? value) → bool**
删除元素。
```dart
queue.remove(1); // {2, 3, 4}
```

**removeFirst() → E**
删除首部元素，并返回。
```dart
queue.removeFirst(); // 1
```

**removeLast() → E**
删除尾部元素，并返回。
```dart
queue.removeFirst(); // 4
```

**removeWhere(bool test(E element)) → void**
删除符合断言的元素。
```dart
queue.removeWhere((item) => item > 1); // {1}
```

**retainWhere(bool test(E element)) → void**
保留符合断言的元素。
```dart
queue.retainWhere((item) => item > 1); // {2, 3, 4}
```
