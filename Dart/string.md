# 字符串
字符串一串UTF-16的编码单位。字符串可能是单行或者多行，可以使用单引号对、双引号对或三引号对来写入。
```dart
'Single quotes';
"Double quotes";
'Double quotes in "single" quotes';
"Single quotes in 'double' quotes";

'''A
multiline
string''';

"""
Another
multiline
string""";
```

使用+操作符来连接字符串，相邻的字符串文字也会自动连接。
```dart
'Dart ' + 'is ' + 'fun!'; // 'Dart is fun!'
'Dart ' 'is ' 'fun!';    // 'Dart is fun!'
```

可以使用**${}**在字符串中插入Dart表达式的值，变量直接输出可以省略大括号。
```dart
var string = 'dartlang';
'$string has ${string.length} letters';
```

可以是索引直接访问字符。
```dart
var str = "hello";
str[0]; // h
```

一般有String、StringBuff和StringSick提供字符串不同的处理方式。

## String
继承自Object，实现了Comparable<String>和Pattern。

#### 构造函数
**String.fromCharCode(int charCode)** 
从一个字符编码构造一个长度为1的字符串
```dart
String.fromCharCode(98); // b
```

**String.fromCharCodes(Iterable<int> charCodes, [int start = 0, int? end])**
从一组字符编码构造一个字符串
```dart
var charCodes = [97, 98, 99, 65, 67];
String.fromCharCodes(charCodes); // 'abcAC'
String.fromCharCodes(charCodes, 3); // 'AC'
String.fromCharCodes(charCodes, 0, 3); // 'abc'
```

**String.fromEnvironment(String name, {String defaultValue=“”})**
获取指定名称的环境变量的值，如果没有该环境变量，则返回默认值。
```dart
// 执行时使用命令：dart run --define=logging=false ...
String.fromEnvironment("logging"); // false
```

#### 属性
**codeUnits -> List<int>**
获取编码集。
```dart
"abcde".codeUnits; // [97, 98, 99, 100, 101]
```

**isEmpty -> bool**
字符串是否为空。
```dart
"".isEmpty; // true
```

**isNotEmpty -> bool**
字符串是否为非空。
```dart
"".isNotEmpty; // false
```

**length -> bool**
获取字符串长度，长度为UTF-16编码单位的字符数量。
```dart
"abcde".length; // 5
'\u{1D11E}'.length; // 2
```

**runes -> Runes**
获取Unicode编码的字符迭代
```dart
"\u{1D11E}".codeUnits; // [55348, 56606]
'\u{1D11E}'.runes; // [119070]
```

#### 方法
**codeUnitAt(int index) -> int**
返回指定位置的一个16位的UTF-16编码单元。
```dart
"abc".codeUnit(0); // 97
```

**compareTo(String other) -> int**
与其他字符串比较，返回一个数值，该值主要用于排序，在顺序排序中，值为负表示当前先于其他，为正表示当前后于其他，为零表示相同。
```dart
"abc".compareTo("aac"); // 1
"abc".compareTo("abc"); // 0
"abc".compareTo("cbc"); // -1
```

**contains(Pattern other, [int startIndex = 0]) -> bool**
判断字符串是否包含该模式，模式可以为字符串或正则。也可以指定匹配的开始位置。
```dart
var string = 'Dart strings';
string.contains('D'); // true
string.contains(RegExp(r'[A-Z]')) // true

string.contains('D', 1); // false
string.contains(RegExp(r'[A-Z]'), 1); // false
```

**startsWith(Pattern pattern, [int index = 0]) → bool**
前缀判断，判断是否在首部包含其他字符串。
```dart
'Dart'.endsWith('D'); // true
```

**endsWith(String other) -> bool**
后缀判断，判断是否在尾部包含其他字符串。
```dart
'Dart'.endsWith('t'); // true
```

**indexOf(Pattern pattern, [int start = 0]) -> int**
返回指定模式所在字符串中**首次出现**的位置，可以指定匹配的开始位置，如果未匹配则返回-1。
```dart
var string = 'Dartisans';
string.indexOf('art'); // 1
string.indexOf(RegExp(r'[A-Z][a-z]')); // 0
string.indexOf(RegExp(r'dart')); // -1
```

**lastIndexOf(Pattern pattern, [int? start]) -> int**
返回指定模式所在字符串中**最后出现**的位置，可以指定匹配的开始位置，如果未匹配则返回-1。
```dart
var string = 'Dartisans';
string.lastIndexOf('a'); // 6
string.lastIndexOf(RegExp(r'a(r|n)')); // 6
string.lastIndexOf(RegExp(r'DART')); // -1
```

**padLeft(int width, [String padding = '']) -> String**
在字符串左边填充指定字符。填充规则如下：
1）如果指定长度小于或等于字符串长度，则不填充
2）如果指定长度大于字符串长度，repeat = 指定长度 - 字符串长度，新字符串 = repeat * padding + 旧字符串
```dart
"abc".padLeft(10, "1"); // 1111111abc
"abc".padLeft(10, "12"); // 12121212121212abc
```

**padRight(int width, [String padding = '']) -> String**
在字符串由边填充指定字符，填充规则如上。
```dart
"abc".padRight(10, "1"); // abc1111111
"abc".padRight(10, "12"); // abc12121212121212
```

**replaceAll(Pattern from, String replace) -> String**
将字符串中符合模式的部分全部替换成指定字符串。
```dart
'resume'.replaceAll(RegExp(r'e'), 'é'); // 'résumé'
```

**replaceAllMapped(Pattern from, String replace(Match match)) → String**
将字符串中符合模式的部分全部替换成指定**生成**的字符串。
```dart
'I have a secret now!'.replaceAllMapped(
	RegExp(r'\b(\w*?)([aeiou]\w*)', caseSensitive: false),
	(Match m) => "${m[2]}${m[1]}${m[1]!.isEmpty ? 'way' : 'ay'}"
); // Iway avehay away ecretsay ownay!
```

**replaceFirst(Pattern from, String to, [int startIndex = 0]) → String**
将字符串中**首次符合**模式的部分替换成指定字符串。并可以指定匹配的开始位置。
```dart
'0.0001'.replaceFirst(RegExp(r'0'), ''); // '.0001'
'0.0001'.replaceFirst(RegExp(r'0'), '7', 1); // '0.7001'
```

**replaceFirstMapped(Pattern from, String replace(Match match), [int startIndex = 0]) → String**
将字符串中**首次符合**模式的部分替换成指定**生成**字符串。并可以指定匹配的开始位置。
```dart
'I have a secret now!'.replaceAllMapped(
	RegExp(r'\b(\w*?)([aeiou]\w*)', caseSensitive: false),
	(Match m) => "${m[2]}${m[1]}${m[1]!.isEmpty ? 'way' : 'ay'}"
); // Iway have a secret now!
```

**replaceRange(int start, int? end, String replacement) → String**
将字符串中指定范围的内容替换指定字符串。0 <= start <= end <= length，如果end为null，end = length
```dart
'good day'.replaceRange(5, null, 'weather'); // good weather
'good day'.replaceRange(1, 3, 'aa'); // gaad day
```

**split(Pattern pattern) → List<String>**
根据指定模式把字符串分割成列表。
```dart
"Hello world".split(" "); // ["Hello", "World"]
"abbc".split(RegExp(r"b*")); // ["a", "a"]
"".split(""); // []
"".split("a"); // [""]
// 分割UTF-16编码单元组时
'\u{1D11E}'.split(''); // ["\ud834", "\udd1e"]
```

**splitMapJoin(Pattern pattern, {String onMatch(Match)?, String onNonMatch(String)?}) → String**
根据指定模式分割字符串，并分别处理匹配和未匹配的部分后合成新的字符串。
```dart
'Eats shoots leaves'.splitMapJoin(
	RegExp(r'shoots'),
	onMatch: (m) => '${m[0]}', 
	onNonMatch: (n) => '*'
); // Result: "*shoots*"
```

**substring(int start, [int? end]) → String**
获取指定范围的子字符串。0 <= start <= end <= length
```dart
var string = 'dartlang';
string.substring(1);    // 'artlang'
string.substring(1, 4); // 'art'
```

**toLowerCase() → String**
字符串全部小写。
```dart
'ALPHABET'.toLowerCase(); // 'alphabet'
'abc'.toLowerCase();      // 'abc'
```

**toUpperCase() → String**
字符串全部大写。
```dart
'alphabet'.toUpperCase(); // 'ALPHABET'
'ABC'.toUpperCase();      // 'ABC'
```

**trim() → String**
移除字符串首尾任何空白字符。
```dart
' Dart '.trim(); // 'Dart'
'\tDart is fun\n'.trim(); // 'Dart is fun'
```

**trimLeft() → String**
移除字符串首部任何空白字符。
```dart
' Dart '.trimLeft(); // 'Dart '
'\tDart is fun\n'.trimLeft(); // 'Dart is fun\n'
```

**trimRight() → String**
移除字符串尾部任何空白字符。
```dart
' Dart '.trimRight(); // ' Dart'
'\tDart is fun\n'.trimRight(); // '\tDart is fun'
```

## StringBuffer
一个有效连接字符串的类。不同String的地方，StringBuff在进行字符串拼接的时候不会产生新的对象。

#### 构造函数
**StringBuffer([Object content = ""])**
创建一个带初始值的string buffer。
```dart
var buffer = StringBuffer();
var buffer = StringBuffer('buffer');
```

#### 属性
**isEmpty -> bool**
判断buffer是否为空。这是一个常量时间的操作。
```dart
buffer.isEmpty; // false
```

**isNotEmpty -> bool**
判断buffer是否为非空。这是一个常量时间的操作。
```dart
buffer.isNotEmpty; // true
```

**length -> bool**
返回到目前为止已经积累的内容的长度。这是一个常量时间的操作。
```dart
buffer.length; // 6
```

#### 方法
**clear() -> void**
清空这个字符串buffer。
```dart
var buffer = StringBuffer('buffer');
buffer.clear(); // ''
```

**toString() -> String**
将buffer的内容转换成单字符串。
```dart
var buffer = StringBuffer('buffer');
buffer.toString(); // 'buffer'
```

**write(Object? object) → void**
在buffer中写入对象的字符串表示。也就是调用对象的toString()后再写入。
```dart
var buffer = StringBuffer("buffer ");
buffer.write(Object()); // buffer Instance of 'Object'
```

**writeAll(Iterable objects, [String separator = ""]) → void**
在buffer中写入一组对象的字符串表示，并可以指定分隔方式。
```dart
var buffer = StringBuffer("buffer ");
buffer.writeAll(["is ", Object()]); // buffer is Instance of 'Object'
buffer.writeAll(["is", "a", Object()], " && "); // buffer is && a && Instance of 'Object'
```

**writeCharCode(int charCode) → void**
在buffer中写入一个charCode对于的字符。
```dart
var buffer = StringBuffer("buffer ");
buffer.writeCharCode(98); // buffer b
```

**writeln([Object? obj = ""]) → void**
在buffer中吸入一个指定对象添加”\n“来换行。
```dart
var buffer = StringBuffer("buffer ");
buffer.writeln("a");
buffer.writeln("new line");
// buffer a 
// new line
//
```
