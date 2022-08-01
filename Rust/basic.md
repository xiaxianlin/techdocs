# Rust基础

### 变量

变量通过 **let **关键字来进行声明，并分为不可变变量和可变变量，相同变量名可以重复声明，重复声明后会得到一个全新的变量。不可变只能进行一次赋值，再次赋值会抛出异常。可以通过添加 **mut** 关键字来赋予变量可变属性。变量可先声明，后赋值。**变量不能在全局作用域内声明。**

```rust
let x = 5;
x = 6; // cannot assign twice to immutable variable `x`

let mut y = 5;
y = 6; // is ok.

let z;
z = 5;
```

### 常量

常量通过 **const** 关键字来进行声明，只能赋值一次且不可修改，类似于不可变常量，但是有些许不一样的地方，首先常量不能添加 **mut** 关键字描述，其次常量声明必须声明数据类型，最后常量只能在声明的时候赋值。常量命名必须全部大写，并以下划线连接。**常量可以任意作用域内声明。**建议硬编码的值都通过常量来声明。

```rust
const MAX_TIME: i32 = 120;
```

### 数据类型

在rust中每个值都有明确的数据类型。主要有两种数据类型：**scalar** 和 **compound** 。rust是静态类型语言，因此必须在编译阶段就明确所有变量的数据类型。

##### Scalar Types

scalar类型的变量一般都是一个单独的值。常见的scalar类型有：整型（integer）、浮点型（floating-point number）、布尔值（Boolean）和字符（character）。

整型分为 **有符号(Singed) **和 **无符号（Unsighed）** 并跟长度不同分不同类型。

| 长度    | 有符号 | 无符号 |
| ------- | ------ | ------ |
| 8-bit   | i8     | u8     |
| 16-bit  | i16    | u16    |
| 32-bit  | i32    | u32    |
| 64-bit  | i64    | u64    |
| 128-bit | i128   | u128   |
| arc     | isize  | usize  |

整型在不声明类型的时候默认是**i32**。整型的字面量有Decimal、Hex、Octal、Binary和Byte。其中Decimal可以用_来连接以方便阅读。

```rust
let num: i32 = 123;
let connect_numer = 123_456;
```

浮点型主要有 **f32** 和 **f64** 两种类型。f32是单精度，f64是双精度。浮点型遵循 IEEE-754标准。不指定类型时默认f64。

```rust
let process = 0.56;
let big_count: f64 = 123.456;
```

布尔类型使用关键字 **bool** 修饰。

```rust
let t = true;
let f: bool = false;
```

字符类型使用关键字 char 修饰。

```rust
let c = 'z';
let z = 'ℤ';
let heart_eyed_cat = '😻';
```

##### Compound Types

复合类型的数据是将多个值组合到一个类型里面，内置的复合类型有 **元组（tuple）** 和 **数组（array）**。

元组是将一系列不同数据类型的值组合到一个值里面，使用符号 **()** 来修饰元组。 元组的长度是固定不变的，只要声明过后，长度就不再变化。可以通过析构取值，也可以通过  ``<value>.<index>`` 的形式取值，

```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);
let (x, y, z) = tup;
let x1 = tup.0;
let y1 = tup.1;
let z1 = tup.2;
```

数组是将一系列相同数据类型的值组合到一个值里面，使用符号 **[]** 来修饰数组。数组的长度也是固定不变的。通过 ``<value>[<index>]`` 的形式访问数据。

```rust
let a = [1,2,3,4,5];
let a: [i32, 5] = [1,2,3,4,5];
let a = [3, 5]; // 填充初始值
```

### 函数

##### 声明

通过关键字 **fn** 进行函数声明，函数包含函数名、参数、函数体、返回值等。Rust 的命名规范是使用下划线连接。

```rust
fn antoher_function(){}
```

##### 参数

一个函数可以拥有很多个参数，参数的命名依然是下划线连接，并且参数必须显示指定类型。

```rust
fn another_function(x: i32, unit_label: i32){}
```

##### 返回

函数的返回需要声明返回类型，在rust中函数返回数据不需要return关键字，直接在最后一行使用变量或表达式，不写分号即可。

```rust
fn another_function(x: i32) -> i32 {
	x + 1
}
```

##### 调用

直接调用

```rust
another_function(5);
```

##### 入口函数

rust 使用 main 函数作为入口函数。

```rust
fn main(){}
```

### 控制流

#### if - else if - if 判断

在 rust 里面 if 判断后面没有括号，直接跟随表达式，可以通过 `let-if` 获得返回值。

```rust
let number = 2;
if number % 2 == 0 {} 
else if number % 2 == 1 {}
else {}
// 使用 let-if 表达式
let x = if true { 6 } else { 5 }; // x = 6
```

在 if 判断中还有一种 `if let` 语法来处理枚举判断。

```rust
let config_max = Some(3u8);
if let Some(max) = config_max { // max 属于在作用域类创建的变量，把 config_max 赋值 max
  // todo
}
```

##### loop 循环

使用 loop 关键字无限循环一块代码，可以使用 break 退出，可以通过let-if获得返回值。

```rust
loop {
  if something {
    break;
  }
}
// 使用 let-loop 表达式
let x = loop {
  counter += 1;
  if counter == 10 {
    break counter;
  }
}; // x = 10
```

##### while 循环

使用 while 关键字有条件的循环一块代码，可以使用 break 退出。

```rust
while condition {
  // todo
}
```

##### for-in迭代

使用 for-in 可以对实现了迭代功能的对象进行遍历。

```rust
let a = [1, 2, 3];
for e in a {
  // todo
}
```

##### match 控制流

在 rust 中没有 switch 关键字，而是使用 match 来实现了分支匹配。match 匹配大部分时候配合枚举使用。

```rust
enum Coin {
  Penny,
  Nickel,
}
fn value_in_cents(coin: Coin) -> u8 {
  match coin {
    Coin::Penny => 1,
    Coin::Nickel => 5,
  }
}
```

如果枚举使用关联数据，需要传入具体类型的数据或者使用 `_` 代替。在 match Option\<T> 的时候必须覆盖 None。否则会抛出异常。

```rust
let a:u32 = 2;
match data {
  None => 0,
  Some(_) => 1,
  Some(a) => a,
}
```

可以使用 `_` 来设置默认分支，如果所有匹配都失败，则进入默认分支。

```rust
match x {
  1 => 1,
  _ => 0,
}
```

多模式匹配

```rust
match x {
    1|2 => true,
    _ => false,
}
```

范围匹配

```rust
match x {
    1..=5 => 5,
    _ => 0,
}
```

结构匹配

```rust
match p {
    Point {x, y: 0} => 'x',
    Point {x: 0, y} => 'y',
    Point {x, y} => 'xy',
}
```



同样也能用 `let-match` 语法进行匹配赋值。

