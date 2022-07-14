# 引用的生命周期

在 rust 中生命周期是另外一种常用的**泛型**，主要用来修饰引用。生命周期不是确保类型具有预期行为，而是确保引用在使用时保持有效。每个引用都有一个自己的生命周期，并且在作用域内是有效的。大部分时候生命周期都是隐式的，由编译器进行推断。只有在可能有多种类型时，才必须注释类型。以类似的方式，当引用的生命周期可以以几种不同的方式关联时，就必须注释生命周期。 Rust 要求使用通用生命周期参数来注释关系，以确保在运行时使用的实际引用肯定是有效的。

##### 防止悬垂引用

在其他语言中类似 JS 这样的，可以利用闭包在作用域外拿到作用域内的应用，如果回收机制不完善会出现引用悬垂。

```rust
fn main() {
  let r;
  {
    let x = 5;
    r = &x; // Error: `x` does not live long enough
  }
  println!("{}", r);
}
```

在 rust 中作用域结束的时候会销毁域内所有引用，如果发现引用被域外借用就会编译失败并提示错误。

##### 引用借用检查

在 rust 编译器内有一个借用检查器。主要通过比较作用域来明确借用是否合法。

```rust
fn main() {
  let r;                // ---------+-- 'a
                        //          |
  {                     //          |
    let x = 5;          // -+-- 'b  |
    r = &x;             //  |       |
  }                     // -+       |
                        //          |
  println!("r: {}", r); //          |
}                       // ---------+
```

在这个例子中 r 引用的生命周期 'a 明显比 x 引用的生命周期 'b 长度更长。因此容易出现引用悬垂，所以在编译的时候会提示异常。

##### 生命周期注释语法

生命周期注释不会改变任何引用的生存时间。相反，它们描述了多个引用的生命周期相互之间的关系，而不影响生命周期。正如签名指定泛型类型参数时函数可以接受任何类型一样，函数可以通过指定泛型生命周期参数来接受具有任何生命周期的引用。

生命周期注释必须以 `'` 开始，通常采用全小写，并且非常短。通常大家都是用 `'a` 来注释一个生命周期。

```rust
&i32        // 引用
&'a i32     // 显式生命周期的引用
&'a mut i32 // 显式生命周期的可变引用
```

##### 函数中的生命周期

在函数中注释生命周期类似于泛型，写法是一致的，只是语法不同，也是在函数名后加 `<>`，并在里面注释生命周期变量。生命周期是一个用来约束函数参数和返回值的关系。

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
  if x.len() > y.len() {
    x
  } else {
    y
  }
}
```

通过注释生命周期来约束引用，在 longest 函数里面只要两个参数都有效，返回的引用就有效，这意味着函数返回的引用的最长生命周期与传入的引用的生命周期中的较小者相同。这样就约束了参数和返回之间的关系。在此函数中指定生命周期参数时，不会更改传入或返回的任何值的生命周期。生命周期注释不会进入函数体，只是出现在参数和返回值。

```rust
fn main() {
  let string1 = String::from("long string is long");
  let result;
  {
    let string2 = String::from("xyz");
    result = longest(string1.as_str(), string2.as_str()); // Error: borrowed value does not live long enough
  } // -`string2` dropped here while still borrowed
  println!("The longest string is {}", result);
}
```

当生命周期一旦声明，函数返回的引用必须注释上这个生命周期。

```rust
fn longest<'a>(x: &str, y: &str) -> &str { // missing lifetime specifier，
    let result = String::from("really long string");
    result.as_str() 
}
```

当生命周期一旦注释在函数上，返回的引用必须与某个参数有关联。

```rust
fn longest<'a>(x: &str, y: &str) -> &'a str {
    let result = String::from("really long string");
    result.as_str() // 在这里 result 会被销毁，引用无法外借，所以编译异常
}
```

对于多个引用参数可以声明多个生命周期，再分别注释不同的参数.

```rust
fn foo<'a, 'b>(a: &'a str, b: &'b str) -> &'b str {
    b
}
```

返回值与参数关联的生命周期一致。

```rust
fn foo<'a, 'b>(a: &'a str, b: &'b str) -> &'b str { //this parameter and the return type are declared with different lifetimes
    a // but data from `a` is returned here
}
```

##### Struct 的生命周期

同样当 struct 中存在引用字段的时候，也可以进行生命周期注释。如果一旦声明了生命周期，所有引用类型字段必须注释生命周期。

```rust
struct User<'a, T> {
    name: &'a str,
    data: &T, // Error: missing lifetime specifier
}
```

生命周期的计算方法是和函数一致，struct 中字段的最短生命周期就是 struct 的生命周期长度。

```rust
struct Data {
  count: i32,
}

fn main() {
  let data = Data { count: 0 };
  let user;
  {
    let name = String::from("test");
    user = User {
      name: &name, // Error: borrowed value does not live long enough
      data: &data,
    }
  } // - `name` dropped here while still borrowed
  println!("{:?}", user); // ---- borrow later used here
}
```

##### 方法的生命周期

在使用 `impl` 实现某个 struct 的时候，如果 struct 声明了生命周期，那么 impl 也需要声明一个，并传递给 struct。在实现的方法也可以手动声明自己的生命周期。

```rust
impl<'a, T> User<'a, T> {
  fn get_name(&self) -> &str {
    self.name
  }
  fn get_alias<'b'>(&self, alias: &'b str) -> &'b str {
    alias
  }
}
```

##### 生命周期注释省略规则

函数或方法参数的生命周期称为输入生命周期，返回值的生命周期称为输出生命周期。

1) 为每个引用参数都分配一个生命周期，并且没有输出生命周期参数

```rust
fn rule1<'a>(s: &'a str) // fn rule1(s: &str)
fn rule1<'a, 'b>(s: &'a str, m: &'b str) // fn rule1(s: &str, m: &str)
```

2. 如果只有一个输入生命周期参数，则将该生命周期分配给输出生命周期参数

```rust
fn rule2<'a>(x: &'a i32) -> &'a i32 // fn rule2(x: &i32) -> &i32
fn rule2<'a>(s: &'a str) -> &str // 默认 'a 注释给返回值
```

3. 如果有多个输入生命周期参数，但其中一个是 `&self` 或 `&mut self`，因为这是一个方法，则将 self 的生命周期分配给输出生命周期参数。

```rust
impl<'a, T> User<'a, T> {
  fn get_name(&self) -> &str {
    self.name
  }
}
```

##### 静态生命周期

通过 `'static` 声明静态生命周期可以让应用在整个程序运行期间都存在。

```rust
let s: &'static str = "I have a static lifetime.";
```

