# 错误处理

### Unrecoverable Error

使用宏 `panic!` 可以手动的中断程序，该操作造成的错误是不可以被恢复，程序中断后不会立刻释放和清除 Stack（堆）数据，等系统来回收。如果需要直接释放需要在 `Cargo.toml` 里加一个配置。

```toml
[profile.release]
panic = 'abort'
```

调用一个 panic。

```rust
fn main(){
  panic!("crash and run");
}
```

##### 错误回溯

可以使用 `RUST_BACKTRACE` 配置进行错误回溯，可以追踪错误链路。

```sh
RUST_BACKTRACE=1 cargo run
```

### Recoverable Error

在程序中很多时候错误是需要被捕获并恢复的，rust 中使用了 `Result` 来处理可恢复的错误。例如打开一个文件：

```rust
let f = File::open("hello.txt");
let f = match f {
  Ok(file) => file,
  Err(error) => panic!("Problem opening the file: {:?}", error),
}
```

##### 匹配不同类型的错误

由于发生错误的原因可能有很多种，每种会返回不同类型的错误，就需要对类型错误进行匹配。

```rust
let f = match f {
  Ok(file) => file,
  Err(error) => match error.kind() {
    ErrorKind::NotFound => match File::create("hello.txt") {
      Ok(fc) => fc,
      Err(e) => panic!("Problem creating the file: {:?}", error)
    },
    other_error => {
      panic!("Problem opening the file: {:?}", error);
    }
  },
}
```

##### 使用 unwrap_or_else 替代 match

```rust
let f = File::open("hello.txt").unwrap_or_else(|error| {
  if error.kind() == ErrorKind::NotFound {
    File::create("hello.txt").unwrap_or_else(|error| {
      panic!("Problem creating the file: {:?}", error)
    }) // 这里不能有;
  }else{
    panic!("Problem opening the file: {:?}", error);
  }
});
```

##### 使用 unwrap 和 except 简化 Result

如果 Result 是 Err 时，unwrap 会自动调用 panic! 宏。

```rust
File::open("hello.txt").unwrap(); 
// called `Result::unwrap()` on an `Err` value: Error {repr: Os { code: 2, message: "No such file or directory" } }
```

如果是用 except 可以提供更好的错误信息，方便问题排查。

```rust
File::open("hello.txt").expect("Failed to open hello.txt"); 
// Failed to open hello.txt: Error { repr: Os { code:2, message: "No such file or directory" } }
```

##### 错误传导

在实际编写中，很多错误处理后不需要中断程序，而是把错误封装后交给调用者处理。只需要将执行结果封装成 Result 并返回即可。

```rust
fn read_username_from_file() -> Result<String, io::Error> {
  let f = File::open("hello.txt");
  let mut f = match f {
    Ok(file) => file,
    Err(e) => return Err(e),
  };

  let mut s = String::new();
  match f.read_to_string(&mut s) {
    Ok(_) => Ok(s),
    Err(e) => Err(e),
  }
}
```

##### 使用 ? 处理错误

rust 提供 `?` 操作符来简化了错误的处理。在表达式的尾部加上 ? 操作符表示获取错误后自动返回。

```rust
fn read_username_from_file() -> Result<String, io::Error> {
  let mut s = String::new();
  File::open("hello.txt")?.read_to_string(&mut s)?;
  Ok(s)
}
```

只有当函数返回的类型为 Result 和 Option 时才可以使用 ? 操作符。