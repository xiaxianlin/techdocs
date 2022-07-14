# 常用宏（Macro）

### print! 和 println!

打印信息。

```rust
println!("{}", "test");
println!("{?:}", struct);
```

### format!

格式化字符串。

```rust
let s1 = String::from("tic");
let s2 = String::from("tac");
let s3 = String::from("toe");

let s = format!("{}-{}-{}", s1, s2, s3); // tic-tac-toe
```

### panic!

抛出一个不可回收的错误。

```rust
panic!("this is a unrecoverable error");
```

### \#[derive(Debug)]

派生一个Debug trait。

