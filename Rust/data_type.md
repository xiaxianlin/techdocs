# 数据类型

### Scalar Type 

- 整型（integer
- 浮点型（floating-point number）
- 布尔值（Boolean）
- 字符（character）



### Compound Type

- 元组（tuple）
- 数组（array）



### Slice Type

切片允许您引用集合中的连续元素序列，而不是整个集合。切片是一种引用，因此它没有所有权。

**字符串切片**

```rust
let s = String::from("hello world");
let hello = &s[0...5];
let sliceBefore = &s[..5]; // = &s[0..5]
let sliceAfter = &s[5..]; // = &s[5..s.len()]

let sr = &s;
let bytes = sr.as_bytes(); // 也是切片类型
```

**数组切片**

```rust
let a = [1, 2, 3, 4, 5];
let slice = &a[1..3];
```



### Struct

Struct类似Tuple类型，也是管理多个关联的值，不同的是，struct可以给值进行命名，这种命名称为struct的字段，并通过字段访问和赋值。因此不需要对值进行排序。

##### 声明一个struct

```rust
struct User {
  active: bool,
  username: String,
  email: String,
  sign_in_count: u64,
}
```

##### 创建一个struct实例

在实例的时候必须对struct所有字段都赋值。

```rust
let user = User {
  email: String::from("someone@example.com"),
  username: String::from("someusername"),
  active: true,
  sign_in_count: 1,
};
```

##### 修改实例某个字段的值

修改实例字段的值，需要实例创建的时候声明为可变类型(mut)。

```rust
user.active = false;
```

##### 其他语法

字段的shorthand和析构。

```rust
fn build_user(email: String, username: String) -> User {
  User {
    email, // shorthand
    username, // shorthand
    active: true,
  	sign_in_count: 1,
  }
}
let user1 = build_user("someone@example.com", "someusername");
let user2 = User {
  email: String::from("someusername"),
  ...user1 // 析构
}
```

##### Tuple Struct

可以把结构声明为元组类型。

```rust
struct Color(i32, i32, i3);
let black = Color(0, 0, 0);
```

##### Unit-Like Struct

空结构。

```rust
struct AlwaysEqual;
let subject = AlwaysEqual;
```

##### 属性和方法调用

一般来说只要引用才能进行属性和方法的调用，直接用变量进行调用rust进行自动应用，可以让代码更好看。

```rust
p.run(&q);
(&p).run(&q);
```



### Enum

枚举主要用来定义类型的机构，使用 **enum** 关键字进行声明。

```rust
enum IpType {
  V4,
  V6,
}
```

##### 枚举值

使用 `::` 调用枚举值。

```rust
let v4 = IpType::V4;
```

##### 关联数据

可以对枚举值进行数据关联。

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```

##### 关联方法

枚举结构也可以通过 **impl** 去关联方法。

```rust
impl Message {
  fn call(&self) {
    // method body would be defined here
  }
}

let m = Message::Write(String::from("hello"));
m.call();
```

##### Option Enum

在 rust 中没有 null 这个类型，需要是用 `Option` 枚举来处理值不存在的情况。

```rust
enum Option<T> {
  None,
  Some(T),
}
```

在使用 Option 枚举时可以省略掉 `Option::` ，直接使用 `Some()` 和 `None`。

```rust
let x: i8 = 5;
let y: Option<i8> = Some(5);
```

如果需要使用 T 进行操作，需要把 Option\<T> 转换过来后再处理。
