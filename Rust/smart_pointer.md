# 智能指针

智能指针是一种数据结构，其作用类似于指针，但也具有额外的元数据和功能。在引用和智能指针之间有一个额外的区别：虽然引用只借用数据，但在许多情况下，智能指针*拥有*它们所指向的数据。

智能指针实现了`Deref`和`Drop`特征。该`Deref` 特征允许智能指针结构的实例表现得像引用，因此可以编写代码以使用引用或智能指针。该`Drop`特征允许自定义当智能指针的实例超出范围时运行的代码。

### Box\<T>

Box 是最直接的智能指针，允许将数据存储在堆上。栈上存储的指向堆上数据的指针。box 只提供了间接存储和堆分配，并没有任何其他特殊的功能。

一般使用场景：

- 当有一个在编译时无法知道其大小的类型，并且想在需要精确大小的上下文中使用该类型的值时
- 当有大量数据并且想要转移所有权但确保在这样做时不会复制数据
- 当想拥有一个值并且只关心它是实现特定 trait 而不是特定 type 的类型时

##### 堆上存储数据

```rust
fn main(){
	let b = Box::new(5);
  println!("b = {}", b);
}
```

可以像范围其他类型数据访问存储在堆上的 box 数据，也如其他拥有数据所有权的值那样，box 在离开作用域时，将被释放，不同的时释放方式不一样，释放过程由 box 本身来完成。但是使用 Box 来单独存放值并没有特别大的意义。

##### 存储递归类型

Rust 在编译时需要知道类型占用的空间，但是递归类型在编译时就无法计算出实际大小。递归类型是指该类型的某个字段的类型是该类型本身，这样就是可能导致无限递归。

```rust
enum List { // Error: recursive type `User` has infinite size
    Cons(i32, List),
    Nil,
}
```

比如 `User` 就进行了类型嵌套，出现了无限递归。这个时候就需要用 Box 来存储。

```rust
enum List {
    Cons(i32, Box<List>),
    Nil,
}
```

因为 `Box<T>` 是一个指针，所以 rust 是明确需要多少来空间来存储的。因为指针的大小并不会根据其指向的数据量而改变。

<img src="_assets_/trpl15-02.svg" style="float:left"/>

### Defer Trait

Rust 可以通过实现 `Defer` trait 来重载解引用运算符 `*`。使用了解引用操作之后智能指针可以被当作常规引用来使用。

```rust
let x = 5;
let y = &x;
assert_eq!(5, x);
// assert_eq!(5, y); no implementation for `{integer} == &{integer}`
assert_eq!(5, *y); // 需要进行解引用之后才能进行对比，数字和引用不能直接断言
```

可以通过模拟 Box 来展示 Defer 的解引用能力。

首先，定一个类型 `MyBox<T>`。

```rust
struct MyBox<T>(T);

impl<T> MyBox<T> {
  fn new(x: T) -> MyBox<T> {
    MyBox(x)
  }
}
```

然后测试一下是否能解引用。

```rust
let x = 5;
let y = MyBox::new(x);
assert_eq!(5, x);
assert_eq!(5, *y); // Error: type `MyBox<{integer}>` cannot be dereferenced
```

发现会抛出异常，无法进行解引用，这时候就需要去实现 Defer 来提供解引用的能力。

```rust
use std::ops::Deref;
impl<T> Deref for MyBox<T> {
  type Target = T;
  fn deref(&self) -> &T {
    &self.0
  }
}
```

没有 `Deref` trait 的话，编译器只会把 `&` 引用类型解引用。`deref` 方法向编译器提供了一种能力：能够获取任何实现了 `Deref` trait 的类型的值，并且可以通过调用这个类型的 `deref` 方法来获取一个解引用方法已知的 `&` 引用。Rust 将 `*` 运算符替换为先调用 `deref` 方法再进行普通解引用的操作。

##### 解引用强制转换

Rust 在函数或方法传参上的一种便利。解引用强制转换只能工作在实现了 Deref trait 的类型上。解引用强制转换将一种类型（A）隐式转换为另外一种类型（B）的引用，因为 A 类型实现了 Deref trait，并且其关联类型是 B 类型。Rust 也提供了 `DerefMut` trait 用于重载可变引用的 `*` 运算符。

```rust
fn hello(name: &str) {
  println!("Hello, {}!", name);
}
fn main() {
  let m = MyBox::new(String::from("Rust"));
  hello(&m);
}
```

Rust 在发现类型和 trait 的实现满足以下三种情况时会进行解引用强制转换：

- 当 `T: Deref<Target=U>` ：从 `&T` 到 `&U`。
- 当 `T: DerefMut<Target=U>` ：从 `&mut T` 到 `&mut U`。
- 当 `T: Deref<Target=U>` ：从 `&mut T` 到 `&U`。

### Drop Trait

通过实现 `Drop` trait 可以在值离开作用域的时候实现一些功能。包含在 prelude 中，使用时无需导入。

```rust
struct CustomSmartPointer {
  data: String,
}

impl Drop for CustomSmartPointer {
  fn drop(&mut self) {
    println!("Dropping CustomSmartPointer with data `{}`!", self.data);
  }
}

fn main() {
  let c = CustomSmartPointer { data: String::from("my stuff") };
  let d = CustomSmartPointer { data: String::from("other stuff") };
  println!("CustomSmartPointers created.");
}
```

Rust 的 `drop` 方法是一个析构函数。不允许显式调用 `drop` 方法，因为在作用结束后 rust 会自动调用。同时 rust 也提供了 `std::mem::drop` 函数来提前调用值的 drop 方法。

```rust
std::mem::drop(c);
```

### Rc\<T>

当出现单个值出现多个所有者的时候，需要启用多所有权，可以通过 `Rc<T>` 类型来处理，此类型称为 **引用次数（reference counting）** 类型，引用计数意味着记录一个值引用的数量来知晓这个值是否仍在被使用。如果某个值有零个引用，就代表没有任何有效引用并可以被清理。

一般用于在堆上分配一些内存供程序的多个部分读取，而且无法在编译时确定程序的哪一部分会最后结束。

##### 使用 Rc\<T> 共享数据

```rust
enum List {
  Cons(i32, Rc<List>),
  Nil,
}

use crate::List::{Cons, Nil};
use std::rc::Rc;

fn main() {
  let a = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));
  let b = Cons(3, Rc::clone(&a));
  let c = Cons(4, Rc::clone(&a));
}
```

Rc 需要通过 use 引入，并提供 new 和 clone 两个方法分别来创建值和增加引用计数。区别于`a.clone()` 和 `Rc::clone(&a)` 的是前者是深拷贝，而后者只是增加引用计数，而 rust 中习惯使用 Rc::clone()。可以使用 `Rc::strong_count()` 去获取引用次数。

##### 避免引用循环

将 `Rc<T>` 缓存 `Weak<T>`。可以通过调用 `Rc::downgrade` 并传递 `Rc<T>` 实例的引用来创建其值的 **弱引用**（*weak reference*）。调用 `Rc::downgrade` 时会得到 `Weak<T>` 类型的智能指针。不同于将 `Rc<T>` 实例的 `strong_count` 加1，调用 `Rc::downgrade` 会将 `weak_count` 加1。`Rc<T>` 类型使用 `weak_count` 来记录其存在多少个 `Weak<T>` 引用，类似于 `strong_count`。其区别在于 `weak_count` 无需计数为 0 就能使 `Rc<T>` 实例被清理。

```rust
use std::rc::{Rc, Weak};
use std::cell::RefCell;

#[derive(Debug)]
struct Node {
  value: i32,
  parent: RefCell<Weak<Node>>,
  children: RefCell<Vec<Rc<Node>>>,
}
```

### RefCell\<T>

`RefCell<T>` 代表其数据的唯一的所有权。对于引用和 `Box<T>`，借用规则的不可变性作用于编译时。对于 `RefCell<T>`，这些不可变性作用于 **运行时**。对于引用，如果违反这些规则，会得到一个编译错误。而对于 `RefCell<T>`，如果违反这些规则程序会 panic 并退出。`RefCell<T>` 正是用于当你确信代码遵守借用规则，而编译器不能理解和确定的时候。

##### 内部可变性

内部可变性（*Interior mutability*）是 Rust 中的一个设计模式，它允许你即使在有不可变引用时也可以改变数据，这通常是借用规则所不允许的。为了改变数据，该模式在数据结构中使用 `unsafe` 代码来模糊 Rust 通常的可变性和借用规则。

`RefCell<T>` 是一个获得内部可变性的方法。

```rust
#[derive(Debug)]
enum List {
  Cons(Rc<RefCell<i32>>, Rc<List>),
  Nil,
}

use crate::List::{Cons, Nil};
use std::rc::Rc;
use std::cell::RefCell;

fn main() {
  let value = Rc::new(RefCell::new(5));

  let a = Rc::new(Cons(Rc::clone(&value), Rc::new(Nil)));

  let b = Cons(Rc::new(RefCell::new(6)), Rc::clone(&a));
  let c = Cons(Rc::new(RefCell::new(10)), Rc::clone(&a));

  *value.borrow_mut() += 10;

  println!("a after = {:?}", a);
  println!("b after = {:?}", b);
  println!("c after = {:?}", c);
}
```

