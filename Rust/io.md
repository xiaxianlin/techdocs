# I/O操作

#### 读取文件

使用 `fs` 模块进行文件操作。

##### read_to_string()

读取文件并转换成字符串。

```rust
use std::fs;
fs::read_to_string(filename);
```

