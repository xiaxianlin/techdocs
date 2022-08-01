# 命令行工具开发

### 读取参数值

通过内置模块 `env` 读取命令行运行的参数，比如执行：`cargo run searchstring hello.txt`

```rust
use std::env;

fn main() {
  let args: Vec<String> = env::args().collect();
  println!("{:?}", args); // ["target/debug/minigrep", "searchstring", "hello.txt"]
}
```

读取环境变量。比如执行：`IGNORE_CASE=1 cargo run to poem.txt`，需要读取 IGNORE_CASE 的值。

```rust
let ignore_case = env::var("IGNORE_CASE").is_ok();
```

### 标准输出流

使用宏 `eprintln!` 可以将信息作为标准流输出，这样在使用 `cargo run > <file>` 进行信息收集的时候，依然能够打印到控制台。
