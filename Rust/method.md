# 方法

方法是一类特殊的函数，函数可以成为独立的方法，而方法是在结构上下文上被定义的，语法基本一致，不同之处就是方法的第一个参数必须是 **调用方法结构的引用（&self）**。方法的调用需要通过结构对象进行调用。

需要通过 **impl** 关键字去实现一个结构，并在 **{}** 内进行方法声明。可以多次使用 impl。

```rust
struct Rectangle {
	width: u32,
  height: u32,
}
impl Rectangle {
  fn area(&self) -> u32 {
    self.width * self.height
  }
}
let rect = Rectangle {
  width: 120,
  height: 40,
}
println!("{}", rect.area()); // 4800
```

### 关联函数

关联函数并不算结构的方法，但是关联依然是在 impl 结构块里面实现，只是函数首个参数不再是 &self，并且调用方式也不是用 `.`，而是使用 `::` 来进行调用，有点类似其他的静态方法。

```rust
impl Rectangle {
  fn square(size: u32) -> Rectangle {
    Rectangle {
      width: size,
      height: size,
    }
  }
}
let rect = Rectangle::square(100);
```

