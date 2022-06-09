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