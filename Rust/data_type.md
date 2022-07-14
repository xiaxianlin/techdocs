# 数据类型

### 基础类型

- 整型（integer
- 浮点型（floating-point number）
- 布尔值（Boolean）
- 字符（character）

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

##### Option

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

##### Result

在 rust 中没有 `try-catch` 语法，而是使用 `Result` 枚举来封装了异常处理。

```rust
enum Result<T, E> {
  Ok(T),
  Err(E),
}
```



### Vector\<T>

类似于数组，但 Vector 的长度是可以可变，Vector 只能存储相同类型的数据。

##### 创建

可以使用 `Vector::new()` 或着用宏 `vec!` 创建一个 Vector 对象。

```rust
let v: Vec<i32> = Vec::new();
let v = vec![1,2,3];
```

##### 更新

需要声明 mut 类型的变量才可以进行更新，主要通过 `push` 等接口来更新。

```rust
let mut v = vec![1, 2, 3];
v.push(4);
```

##### 读取

可以通过 `get` 接口或者使用引用类型进行索引访问。get 接口返回的 Option\<&T> 类型。使用引用类型取值会发生所有权借用，后面再进行更新会编译失败。

```rust
let mut v = vec![1, 2, 3];
let three: i32 = v[2];
let third: &i32 = &v[2];

v.push(4); // Error: cannot borrow `v` as mutable because it is also borrowed as immutable

match v.get(2) {
	Some(third) => third,
  None => 0,
}
```

##### 迭代

Vector 应该是实现了迭代 traits 的，所以可以使用 `for-in` 进行值的迭代访问。

```rust
let mut v = vec![1, 2, 3];
for i in &v {
	println!("{}", i);
}
// 可以每个元素都加 50
for i in &mut v {
  *i += 50;
}
```



### HashMap<K, V>

一种键值对的存储方式。存储位置通过哈希函数计算出来。要使用 HashMap 需要先用 `use std::collections::HashMap` 引入。rust 使用的哈希函数叫 `SipHash` 的一种方法。

##### 创建

可以是 `new` 方法直接创建，或者通过迭代的 `zip` 方法加上 `collect` 方法创建。

```rust
let mut scores = HashMap::new();
// 或者
let teams = vec!["blue", "yellow"];
let init_scores = vec![10, 50];
let mut scores: HashMap<_, _> = teams.into_iter().zip(init_scores.into_iter()).collect();
```

##### 访问

通过 `get(&K)` 方法访问 HashMap 存储的值。返回 Option<V> 结果。

```rust
let value = scores.get(&String::from("red")); // Option<98>
match value {
  Some(v) => v,
  None => 0,
}
```

通过迭代的形式遍历 HashMap。

```rust
for (key, value) in &scores {
  println!("key: {}, value: {}", key, value);
}
```

##### 更新

HashMap 提供了一些更新的接口。例如 `insert` 等。insert 之后变量的所有权被借走。

插入一条数据。

```rust
scores.insert(String::from("red"), 98);

let name = String::from("red");
let value = 98;
scores.insert(name, value);
println!("name: {}, value: {}", name, value); // Error: borrow of moved value: `name`
```

覆盖数据，再次使用同样的 key 进行插入可以覆盖原值。

```rust
scores.insert(String::from("red"), 99);
```

当 key 对于的 value 不存在时，插入一条数据。

```rust
scores.entry(String::from("black")).or_insert(60);
```

通过迭代的形式修改 Value。

```rust
for (key, value) in &mut scores {
	*value += 100; // 给每个值都加100
}
```



### Generic

泛型，是一种可以减少代码重复的有效数据类型，一种抽象的数据，只有在具体调用的时候才会知道是什么类型。一般不同类型拥有相同逻辑时使用。使用 `<>` 修饰泛型类型，泛型一般采用单个大写字母表示。

在函数中使用。

```rust
fn compare<T: std::cmp::PartialOrd>(a:T, b:T) -> bool {
  a > b
}
compare(1, 2); // T as i32
compare(1.2, 2.3); // T as f64
```

在 struct 里面使用。

```rust
struct Point<T> {
  x: T,
  y: T,
}
let integer = Point { x: 5, y: 10 };
let float = Point { x: 1.0, y: 4.0 };
```

使用多个泛型。

```rust
struct Point<T, U> {
  x: T,
  y: U,
}
let point = Point { x: 5, y: 10.0 };
```

在 enum 中使用。

```rust
enum Result<T, E> {
  Ok(T),
  Err(E),
}
```

在 impl 中使用。

```rust
impl<X1, Y1> Point<X1, Y1> {
  fn mixup<X2, Y2>(self, other: Point<X2, Y2>) -> Point<X1, Y2> {
    Point {
      x: self.x,
      y: other.y,
    }
  }
}
```



### Trait

Trait 定义了特定类型具有并可以与其他类型共享的功能。可以使用 *traits* 以抽象的方式定义共享行为。可以使用 *trait bounds* 来指定泛型类型可以是具有特定行为的任何类型。在其他语言中，trait 被叫做 interface，但是还是有一些不同之处。

##### 定义

使用 `trait` 关键字进行定义， trait 内部的方法可以是抽象方法，也可以具体实现的默认方法。

```rust
trait Summary {
  fn summarize(&self) -> String;
  fn create_time(&self) -> i32 {
    1
  };
}
```

##### 实现

使用 `impl-for` 语法为其他类型实现 trait。在实现 trait 的时候必须实现 trait 的所有抽象方法。

```rust
struct Article {
  pub title: String,
  pub content: String,
}

struct Tweet {
  pub username: String;
  pub contetn: String;
}

impl Summary for Article {
  fn summarize(&self) -> String {
     format!("{}, {}", self.title, self.content)
  }
}

impl Summary for Tweet {
  fn summarize(&self) -> String {
     format!("{}, {}", self.username, self.content)
  }
}

```

##### 作为函数参数类型

Trait 可以做函数参数进行类型声明，在调用的时候使用实现了 trait 的类型传入进来。

```rust
pub fn notify(item: &impl Summary) {
  println!("{}", item.summarize());
}
```

##### 约束泛型

在使用泛型时可以使用 trait 对泛型进行约束。

```rust
pub fn notify<T: Summary>(item: &T) {
  println!("{}", item.summarize());
}
```

可以同时使用多个 trait 进行约束。进行多 trait 约束时，传入的值必须实现多 trait。

```rust
pub fn notify(item: &impl Summary + Display) {
  println!("{}", item.summarize());
}
pub fn notify<T: Summary + Display>(item: &T) {
  println!("{}", item.summarize());
}
```

可以使用 `where` 关键字对多个泛型分别约束，这样会更清晰。

```rust
fn some_function<T, U>(t: &T, u: &U) -> i32
	where T: Display + Clone, U: Clone + Debug {
    
}
```

##### 作为函数返回值类型

可以使用 trait 作为函数的返回值类型。可以使用多个 trait 组合。

```rust
fn returns_summarizable() -> impl Summary + Display {
  Tweet {
    username: String::from("Tom"),
    content: String::from("lol~"),
  }
}
```

