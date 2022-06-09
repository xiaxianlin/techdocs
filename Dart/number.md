# 数值
在dart中数值包括整型和浮点型，主要有num、int和double三个类，其中numw为父类，int、double为子类。处理特别大的整数时，可以使用BigInt类，使用方式和int一致。
```dart
var i = 12;
var d = 12.12;
var i8 = 0x12;
var e = 12.12e+3;
```

## num
num是抽象类，没有构造函数。

#### 属性
**hashCode → int**
返回数值对象的hashCode。
```dart
12.hasdCode; // 12
```

**isFinite → bool**
判断数值是否有限。
```dart
12.isFinite; // true
```

**isInfinite → bool**
判断数值是否无限。任何数除以0都可以得到double.infinite。而double.infinite本身也是一个特殊数值。
```dart
var a = 1 / 0;
a.isInfinite; // true
```

**isNaN → bool**
判断数值是否不是一个数值。在数值中只有double.nan进行isNaN判断为true，其余皆为false。
```dart
12.isNaN; // fasle
double.nan.isNaN;  // true
```

**isNegative → bool**
判断是否为负。
```dart
12.isNegative; // false
(-12).isNegative; // true
```

**sign → num**
一个特殊的标记，数值大于0则返回1，等于0返回0，小于0返回-1，nan返回NaN
```dart
12.sign; // 1
0.sign; // 0
(-12).sign; // -1
double.nan.sign; // NaN
12.12.sign; // 1.0
(-12.12).sign; // -1.0
```

#### 方法
**abs() → num**
获取数值的绝对值。
```dart
12.abs(); // 12
(-12).abs(); // 12
```

**ceil() → int**
获取不小于自己的最小整数。数值必须是有限的。
```dart
12.ceil(); // 12
12.12.ceil(); // 13
(-12.12).ceil(); // -12
```

**ceilToDouble() → double**
获取不小于自己的最小浮点数。数值必须是有限的。
```dart
12.ceilToDouble(); // 12.0
12.12.ceilToDouble(); // 13.0
(-12.12).ceil(); // -12.0
```

**clamp(num lowerLimit, num upperLimit) → num**
在自己、lowerLimit和upperLimit取中间值。
```dart
12.clamp(10, 14); // 12
12.clamp(0, 10); // 10
12.clamp(14, 20); //14
```

**compareTo(num other) → int**
与其他数值进行对比。该方法主要用于排序。
```dart
1.compareTo(2); // -1
2.compareTo(1); // 1
1.compareTo(1); // 0
// 特殊值的对比
(-0.0).compareTo(0.0); // -1
double.nan.compareTo(double.nan); // 0
double.infinity.compareTo(double.nan); // -1
```

**floor() → int**
获取不大于自己的最小整数。数值必须是有限的。
```dart
12.floor(); // 12
12.12.floor(); // 12
(-12.12).floor(); // -13
```

**floorToDouble() → double**
获取不大于自己的最小浮点数。数值必须是有限的。
```dart
12.floorToDouble(); // 12.0
12.12.floorToDouble(); // 12.0
(-12.12).floorToDouble(); // -13.0
```

**remainder(num other) → num**
this被other截断除去的剩余部分。结果计算公式：this - (this ~/ other) * other。
```dart
12.remainder(7); // 5
12.remainder(2); // 0
12.remainder(20); // 12
```

**round() → int**
返回最靠近当前值的整数。
```dart
3.2.round(); // 3
3.5.round();  // 4
(-3.2).round(); // -3
(-3.5).round(); // -4
```

**roundToDouble() → double**
返回最靠近当前值的浮点数。
```dart
3.2.roundToDouble(); // 3.0
3.5.roundToDouble();  // 4.0
(-3.2).roundToDouble(); // -3.0
(-3.5).roundToDouble(); // -4.0
```

**toDouble() → double**
把数值转成浮点型。
```dart
12.toDouble(); // 12.0
12.12.toDouble(); // 12.12
```

**toInt() → int**
把数值转成整型。
```dart
12.toInt(); // 12
12.12.toInt(); // 12
```

**toString() → String**
把数值转成字符串。
```dart
12.toString(); // '12'
12.12.toString(); // '12.12'
double.infinity.toString(); // 'Infinity'
double.nan.toString(); // 'NaN'
```

**toStringAsExponential([int? fractionDigits]) → String**
把数值转成科学计数法的字符串。
```dart
1.toStringAsExponential(); // 1e+0
1.toStringAsExponential(3); // 1.000e+0
123456.toStringAsExponential(); // 1.23456e+5
123456.toStringAsExponential(3); // 1.235e+5
123.toStringAsExponential(0); // 1e+2
```

**toStringAsFixed(int fractionDigits) → String**
把数值转成指定小数长度的字符串。
```dart
1.toStringAsFixed(3); // 1.000
(4321.12345678).toStringAsFixed(3); // 4321.123
(4321.12345678).toStringAsFixed(5); // 4321.12346
123456789012345.toStringAsFixed(3); // 123456789012345.000
10000000000000000.toStringAsFixed(4); // 10000000000000000.0000
5.25.toStringAsFixed(0); // 5
```

**toStringAsPrecision(int precision) → String**
把数值转成指定精度的字符串。
```dart
1.toStringAsPrecision(2); // 1.0
1e15.toStringAsPrecision(3); // 1.00e+15
1234567.toStringAsPrecision(3); // 1.23e+6
1234567.toStringAsPrecision(9); // 1234567.00
12345678901234567890.toStringAsPrecision(20); // 12345678901234567168
12345678901234567890.toStringAsPrecision(14); // 1.2345678901235e+19
0.00000012345.toStringAsPrecision(15); // 1.23450000000000e-7
0.0000012345.toStringAsPrecision(15);  // 0.00000123450000000000
```

**truncate() → int**
把数值的小数位截断后转成整数。
```dart
12.truncate(); // 12
12.12.truncate(); // 12
(-12.12).truncate(); // -12
```

**truncateToDouble() → double**
把数值的小数位截断后转成浮点数。
```dart
12.truncateToDouble(); // 12.0
12.12.truncateToDouble(); // 12.0
(-12.12).truncateToDouble(); // -12.0
```

#### 静态方法
**parse(String input, [num onError(String input)?]) → num**
将字符串转换成数值，可以指定转换失败后获得默认值。onError已经被弃用。
```dart
num.parse("12"); // 12
num.parse("a12", (input) => 10); // 10
```

**tryParse(String input) → num?**
尝试将字符串转换成数值，如果转换失败则返回null。
```dart
num.tryParse("12"); // 12
num.tryParse("a12"); // null
```

## int
整型类。继承于num。int的默认实现是64位二进制补码整数，其操作会在溢出时返回到该范围。

#### 构造函数
**int.fromEnvironment(String name, {int defaultValue = 0})**
获取指定名称的环境变量的值，如果没有该环境变量，则返回默认值。
```dart
// 执行时使用命令：dart run --define=level=2 ...
int.fromEnviroment("level"); // 2
```

#### 属性
**bitLength → int**
返回需要存储当前整型的最小字节数。
```dart
1.bitLength; // 1
12.bitLength; // 4
123.bitLength; // 7
(-123).bitLength; // 7
(-12).bitLength; // 4
(-1).bitLength; // 0
```

**isEven → bool**
返回当前整数是否为偶数。
```dart
1.isEven; // false
12.isEven; // true
```

**isOdd → bool**
返回当前整数是否为奇数。
```dart
1.isEven; // true
12.isEven; // false
```

#### 方法
**gcd(int other) → int**
计算最大公约数。
```dart
12.gcd(13); // 1
12.gcd(15); // 3;
```

**modInverse(int modulus) → int**
计算模逆元。计算公式：this * r ≡ 1 (mod modules)，其中r为结果。相当于(this * r) % modules == 1
```dart
3.modInverse(11); // 4
3.modInverse(5); // 2
30.modInverse(15); // Exception: Not coprime 
```

**modPow(int exponent, int modulus) → int**
模幂运算。计算公式：this ^ exponent mod modulus
```dart
2.modPow(8, 15); // 1
```

**toRadixString(int radix) → String**
按照指定的进制数转换成字符串。radix必须为2到36的整数
```dart
128.toRadixString(2); // '10000000'
128.toRadixString(8); // '200'
128.toRadixString(10); // '128'
128.toRadixString(16); // '80'
```

**toSigned(int width) → int**
返回此整数的最低有效宽度位，将最高保留位扩展到符号。这与使用带符号的 2-s 补码表示截断值以适应宽度位相同。返回值在所有高于宽度的位置具有相同的位值。
```dart
                         //     V--sign bit-V
16.toSigned(5) == -16;   //  00010000 -> 11110000
239.toSigned(5) == 15;   //  11101111 -> 00001111
                         // 
```

**toUnsigned(int width) → int**
将此整数的最低有效宽度位作为非负数（即无符号表示）返回。返回值在所有高于宽度的位位置都为零。
```dart
(-1).toUnsigned(5) == 31   // 11111111  ->  00011111
```

#### 静态方法
**parse(String input, {int? radix, int onError(String source)?}) → num**
将字符串转换成数值，可以指定进制数，也可以指定转换失败后获得默认值。onError已经被弃用。
```dart
num.parse("12"); // 12
num.parse("a12", (input) => 10); // 10
```

**tryParse(String input, {int? radix}) → num?**
尝试将字符串转换成数值，可以指定进制数，如果转换失败则返回null。
```dart
num.tryParse("12"); // 12
num.tryParse("a12"); // null
```

## double
双精度浮点型数值。继承自num。无独有属性和方法。

#### 静态常量
**infinity → const double**
无限。
```
1.0 / 0.0
```

**maxFinite → const double**
最大正值。
```
1.7976931348623157e+308
```

**minPositive → const double**
最小正数。
```
5e-324
```

**nan → const double**
不是一个数值。（not a number）
```
0.0 / 0.0
```

**negativeInfinity → const double**
负无限。
```
-infinity
```
