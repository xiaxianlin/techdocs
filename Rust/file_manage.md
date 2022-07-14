# 文件管理

### Package

一个包由一个或多个 crate 提供的功能组成。包含了 `Cargo.toml` 描述文件，用于描述构建 crate。

##### 引入外部包

在 `Cargo.toml` 文件里面的 `[dependencies]` 下面写入格式为 `<package_name> = "version" ` 即可导入外部包。

```toml
[dependencies]
rand = "0.8.3"
```

### Crate

Crate 可以是二进制 crate 或库 crate。二进制 crate 是可以编译成可执行文件的程序，必须有一个名为 `main` 的函数，该函数提供了执行入口。库 crate 不提供 `main` 函数，只提供功能。

在项目里面由 *src/main.rs* 提供一个二进制 crate，如果存在 *src/lib.rs* 文件，则多提供了一个库 crate。

### Module

在 rust 中每个文件可以视为一个模块，如果想要让一个目录变成模块，需要在该目录下写入一个 **mod.rs** 文件。

使用 `use` 可以把一个路径代入模块， 使用 `pub` 可以在模块中对外暴露数据。

##### 声明模块

使用关键字 `mod` 进行模块声明，声明后 rust 会去寻找 *\<module>.rs* 或者 *\<module>/mod.rs* 文件。

```rust
// src
// |__ main.rs
// |__ example.rs
// |__ example
//     |__ mod.rs
mod example;
```

##### 声明子模块

可以在模块内使用 `mod` 声明子模块，子模块的路径寻找方式和模块一致。子模块在模块内默认是私的，需要通过 `pub` 关键字进行公共元素声明。声明成公共后，其他模块可以通过模块访问该子模块。

```rust
pub mod example;
```

##### 引入模块

使用 `use` 关键字来引入模块，通过模块路径引入，路径的表达方式为 `module::sub_module::<...>` 的形式。

```rust
mod example;
use example::test;
```

在引入模块的时候，可以使用 ***绝对路径*** 和 ***相对路径 ***两种形态。绝对路径是从 `crate` 的根目录开始寻址，相对路径是从当前模块开始寻址。绝对路径访问一般用于跨模块使用。在相对路径可以使用 `self` 和 `super` 来寻址，一般情况下 self 可以省略。

```rust
crate::example::test; // 绝对路径
example::test; // 相对路径

fn deliver_order() {}

mod back_of_house {
  fn fix_incorrect_order() {
    cook_order();
    super::deliver_order(); // super 的相对路径
  }
  fn cook_order() {}
}
```

引入的模块只能在当前作用域使用。可以使用 `pub use` 将引入的模块再次公共化。

```rust
mod front_of_house {
  pub mod hosting {
    pub fn add_to_waitlist() {}
  }
}
mod customer {
  pub use crate::front_of_house::hosting;
  pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
  }
}
fn main() {
  // hosting 被挂载到 customer 模块上了
  customer::hosting::add_to_waitlist();
}

```

##### 使用模块元素

在引入模块可以根据 `module::<element>` 的使用模块对外暴露的元素。属性无法被暴露。

```rust
test::run();
```

##### 块级模块

块级模块主要是用来在同一文件内代码进行分组的时候使用。块级模块也可以作为子模块对外暴露。

```rust
mod test {
  pub mod app {
    pub fn run(){
      println!("run");
    }
  }
}
test::app::run();
```

##### As 别名

使用 `as` 关键字可以对模块进行别名处理。

```rust
use std::io::Result  as IoResult;
fn read() -> IoResult {}
```

##### 使用外部包的模块

在 `Cargo.toml` 里面引入外部包之后，可以用 use 引入外部包的模块。

```rust
use rand::Rng;
```

### Path

Path 是模块的路径，有相对路径和绝对路径。相对路径是从当前模块开始计算，一般有关键字 `super` 和 `self` ， self 可以被省略。绝对路径使用 `crate::` 开始表示从 crate 的根目录开始计算。

##### 使用嵌套路径

嵌套路径可以在同一个父模块下用 `{}` 形式罗列所有子模块，可以让引入路径更简洁。

```rust
use std::{cmp::Orderingg, io};
use std::io::{self, Write}; // self 表示引入 io 本身
```

##### 使用 * 引入

使用 `::*` 的形式可以引入模块的所有公共元素。

```rust
use std::collections::*;
```

