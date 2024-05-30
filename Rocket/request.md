# 请求

### 数据格式（Format）

通过路由 `format` 属性设置来指定请求数据的处理格式。format 属性会去匹配请求头里面的 `Content-Type`。

```rust
#[post("/user", format = "application/json", data = "<user>")]
fn new_user(user: User) { /* ... */ }
```

如果设置 `data` 属性，那么处理函数中相同名称的参数必须实现 `FromData` ，具体实现方式可以参考[示例](https://api.rocket.rs/v0.5-rc/rocket/data/trait.FromData.html#example)。

在指定 format 的数据时， 其类型为 `MediaType`，也可以使用缩写。

### Body 数据

Body 数据的处理，就像 Rocket 的大部分内容一样，是类型导向的。要指示处理程序需要正文数据，使用 `data = "<param>"` 对其进行注释，其中 param 是处理程序中的参数。参数的类型必须实现 FromData 特征。

##### JSON

使用官方内置的解析包。

```
[dependencies]
rocket = { version = "0.5.0-rc.2", features = ["json"] }
rocket_dyn_templates = { version = "0.1.0-rc.2", features = ["tera"] }
```

先对类型派生 `Serialize` 和 `Deserialize`，实现序列化和反序列化。在处理函数上参数类型用 `Json<T>` 声明，这样 rocket 就会自动去解析数据。

```rust
use rocket::serde::json::Json;
use serde::{Deserialize, Serialize};

#[derive(Debug, Serialize, Deserialize)]
struct User {
    name: String,
    age: i32,
}

#[post("/user", format = "json", data = "<user>")]
pub fn new_user(user: Json<User>) -> String {
    format!("User: {:?}", user)
}
```

##### 临时文件

TempFile 数据保护将数据直接流式传输到一个临时文件，然后可以将其持久化。

```rust
use rocket::fs::TempFile;

#[post("/upload", format = "plain", data = "<file>")]
async fn upload(mut file: TempFile<'_>) -> std::io::Result<()> {
    file.persist_to(permanent_location).await
}
```

##### 数据流

当需要直接处理传入的数据。例如，将传入数据流式传输到某个接收器。 Rocket 通过 Data 类型来处理这类操作。

```rust
use rocket::tokio;

use rocket::data::{Data, ToByteUnit};

#[post("/debug", data = "<data>")]
async fn debug(data: Data<'_>) -> std::io::Result<()> {
    data.open(512.kibibytes()).stream_to(tokio::io::stdout()).await?;
    Ok(())
}
```

### 表单

Rocket支持开箱即用的`multipart`和`x-www-form-urlencoded`表单，由`Form`数据保护和可派生`FromForm`特性启用。只要其通用参数实现 FromForm 特征，表单就是数据保护。

##### 简单表单对象

```rust
use rocket::form::Form;

#[derive(FromForm)]
pub struct Task<'r> {
    complete: bool,
    name: &'r str,
}

#[post("/todo", data = "<task>")]
pub fn todo(task: Form<Task<'_>>) -> String {
    format!("complete: {} \name: {}", task.complete, task.name)
}
```

##### 文件上传（Multipart）

Multipart 表单以透明方式处理，无需额外工作。大多数 FromForm 类型可以从传入的数据流中解析自己。例如，这是一个使用 TempFile 接受分段文件上传的表单和路由：

```rust
use rocket::form::Form;
use rocket::fs::TempFile;

#[derive(FromForm)]
struct Upload<'r> {
    save: bool,
    file: TempFile<'r>,
}

#[post("/upload", data = "<upload>")]
fn upload_form(upload: Form<Upload<'_>>) { /* .. */ }
```

##### 解析策略

Rocket 的 FromForm 解析默认是宽松的：一个 Form&lt;T&gt; 将从传入的表单成功解析，即使它包含额外的、重复的或缺少的字段。多余的或重复的将被忽略，不会对字段进行验证或解析，并且缺失的字段会在可用时用默认值填充。要更改此行为并使表单解析严格，请使用 Form&lt;Strict&gt; 数据类型，如果有任何额外或缺失的字段，它会发出错误，而不考虑默认值。

可以在使用 `Form<T>` 的任何地方使用 `Form<Strict<T>>`。实现 FromForm 也需要它的泛型参数。例如，可以简单地将 `Form<T>` 替换为上面的 `Form<Strict<T>>` 以获得严格解析：

```rust
use rocket::form::{Form, Strict};

#[post("/todo", data = "<task>")]
fn new(task: Form<Strict<Task<'_>>>) { /* .. */ }
```

`Strict` 也可用于使单个字段严格，同时保持整体结构和剩余字段宽松：

```rust
#[derive(FromForm)]
struct Input {
    required: Strict<bool>,
    uses_default: bool
}

#[post("/", data = "<input>")]
fn new(input: Form<Input>) { /* .. */ }
```

`Lenient` 是 `Strict` 的 lenient 类比，它强制解析是 lenient。 `Form` 默认情况下是宽松的，因此 `Form<Lenient<T>>` 是多余的，但 `Lenient` 可用于将严格解析覆盖为 lenient：`Option<Lenient<T>>`。

##### 默认值

表单守卫可以指定在缺少字段时使用的默认值。仅当解析宽松时才使用默认值。严格时，所有错误（包括缺失字段）都会直接传播。一些具有默认值的类型包括 bool，默认为 false，对复选框很有用，`Option<T>`，默认为 `None`，以及 `form::Result`，默认为 `Err(Missing)` 或以其他方式在 `Err of Errors<' 中收集错误_>`。可以像使用任何其他表单保护一样使用默认保护：

```rust
use rocket::form::{self, Errors};

#[derive(FromForm)]
struct MyForm<'v> {
    maybe_string: Option<&'v str>,
    ok_or_error: form::Result<'v, Vec<&'v str>>,
    here_or_false: bool,
}
```

可以使用 `#[field(default = expr)]` 字段属性覆盖或取消设置默认值。如果 `expr` 不是字面意义上的 `None`，则该参数将字段的默认值设置为 `expr.into()`。如果 `expr` 为 `None`，则该参数取消设置字段的默认值（如果有）。

```rust
#[derive(FromForm)]
struct MyForm {
    #[field(default = "hello")]
    greeting: String,
    #[field(default = None)]
    is_friendly: bool,
}
```

##### 字段重命名

默认情况下，Rocket 将传入表单字段的名称与结构字段的名称匹配。虽然这种行为很典型，但可能还需要为表单字段和结构字段使用不同的名称，同时仍按预期进行解析。可以通过使用一个或多个`#[field(name = "name")]` 或 `#[field(name = uncased("name")]` 字段注释来要求 Rocket 为给定结构字段查找不同的表单字段。不区分大小写的变体不区分大小写地匹配字段名称。

```rust
#[derive(FromForm)]
struct External<'r> {
    #[field(name = "first-Name")]
    first_name: &'r str
}
```

忽略大小写

```rust
#[derive(FromForm)]
struct External<'r> {
    #[field(name = uncased("firstName"))]
    first_name: &'r str
}
```

多个重命名，可以匹配任意一个

```rust
#[derive(FromForm)]
struct External<'r> {
    #[field(name = uncased("first-name"))]
    #[field(name = uncased("first_name"))]
    #[field(name = uncased("firstname"))]
    first_name: &'r str
}
```

##### 字段校验

通过 `#[field(validate)]` 属性可以对表单字段进行临时验证。

```rust
#[derive(FromForm)]
struct Person {
    #[field(validate = range(21..))]
    age: u16
}
```

##### 字段嵌套

Rocket 的表单支持允许您的应用程序以任何级别的嵌套和集合来表达任何结构，将字段名称通过分隔符分隔为“键”。和 \[\]，每一个又由 : 分隔成“索引”。换句话说，一个名字有键，一个键有索引，每一个都是其父级的严格子集。

```rust
use rocket::form::FromForm;

#[derive(FromForm)]
struct MyForm<'r> {
    owner: Person<'r>,
    pet: Pet<'r>,
}

#[derive(FromForm)]
struct Person<'r> {
    name: &'r str
}

#[derive(FromForm)]
struct Pet<'r> {
    name: &'r str,
    #[field(validate = eq(true))]
    good_pet: bool,
}
```

要解析为 MyForm，必须提交具有以下字段的表单：

- `owner.name` - string
- `pet.name` - string
- `pet.good_pet` - string

比如路由参数 `"owner.name=Bob&pet.name=Sally&pet.good_pet=on"` 的解析：

```rust
MyForm {
    owner: Person {
        name: "Bob".into()
    },
    pet: Pet {
        name: "Sally".into(),
        good_pet: true,
    }
}
```

注意 `.` 用于分隔每个字段。同样， `[]` 可以用来代替或补充 `.`，比如下面几个路由参数也解析出同样的结果。

- `"owner[name]=Bob&pet[name]=Sally&pet[good_pet]=on"`
- `"owner[name]=Bob&pet[name]=Sally&pet.good_pet=on"`
- `"owner.name=Bob&pet[name]=Sally&pet.good_pet=on"`
- `"pet[name]=Sally&owner.name=Bob&pet.good_pet=on"`

最终，任何层级的嵌套都是被允许的。

##### 向量字段

普通向量类型的解析。

```rust
#[derive(FromForm)]
struct MyForm {
    numbers: Vec<usize>,
}

// These form strings...
"numbers[]=1&numbers[]=2&numbers[]=3",
"numbers[a]=1&numbers[b]=2&numbers[c]=3",
"numbers[a]=1&numbers[b]=2&numbers[a]=3",
"numbers[]=1&numbers[b]=2&numbers[c]=3",
"numbers.0=1&numbers.1=2&numbers[c]=3",
"numbers=1&numbers=2&numbers=3",

// ...parse as this struct:
MyForm {
    numbers: vec![1 ,2, 3]
}
```

向量类型与其他类型的嵌套。

```rust
#[derive(FromForm)]
struct MyForm {
    name: String,
    pets: Vec<Pet>,
}

#[derive(FromForm)]
struct Pet {
    name: String,
    #[field(validate = eq(true))]
    good_pet: bool,
}

// These form strings...
"name=Bob&pets[0].name=Sally&pets[0].good_pet=on",
"name=Bob&pets[sally].name=Sally&pets[sally].good_pet=yes",
"name=Bob&pets[0].name=Sally&pets[1].good_pet=on",
"name=Bob&pets[].name=Sally&pets[].good_pet=on",

// ...parse as this struct:
MyForm {
    name: "Bob".into(),
    pets: vec![Pet { name: "Sally".into(), good_pet: true }],
}
```

二维向量的解析。

```rust
#[derive(FromForm)]
struct MyForm {
    v: Vec<Vec<usize>>,
}

"v=1&v=2&v=3" => MyForm { v: vec![vec![1], vec![2], vec![3]] },
"v[][]=1&v[][]=2&v[][]=3" => MyForm { v: vec![vec![1], vec![2], vec![3]] },
"v[0][]=1&v[0][]=2&v[][]=3" => MyForm { v: vec![vec![1, 2], vec![3]] },
"v[][]=1&v[0][]=2&v[0][]=3" => MyForm { v: vec![vec![1], vec![2, 3]] },
"v[0][]=1&v[0][]=2&v[0][]=3" => MyForm { v: vec![vec![1, 2, 3]] },
"v[0][0]=1&v[0][0]=2&v[0][]=3" => MyForm { v: vec![vec![1, 3]] },
"v[0][0]=1&v[0][0]=2&v[0][0]=3" => MyForm { v: vec![vec![1]] },
```

##### Map 字段

```rust
use std::collections::HashMap;

#[derive(FromForm)]
struct MyForm {
    ids: HashMap<String, usize>,
}

// These form strings...
"ids[a]=1&ids[b]=2",
"ids[b]=2&ids[a]=1",
"ids[a]=1&ids[a]=2&ids[b]=2",
"ids.a=1&ids.b=2",

// ...parse as this struct:
MyForm {
    ids: map! {
        "a" => 1usize,
        "b" => 2usize,
    }
}
```

### Query Strings

查询字符串是 URL 编码的形式，出现在请求的 URL 中。查询参数像路径参数一样声明，但在其他方面则像常规 URL 编码的表单字段一样处理。下表总结了这个类比：

| 路径语法 | 查询语法 | 路径类型 | 查询类型 |
| --- | --- | --- | --- |
| `<param>` | `<param>` | `FromParam` | `FromForm` |
| `<param..>` | `<param..>` | `FromSegments` | `FromForm` |
| `static` | `static` | N/A | N/A |

因为动态参数是表单类型，所以它们可以是单个值、集合、嵌套集合或介于两者之间的任何内容，就像任何其他表单字段一样。

##### 静态参数

如果请求的查询字符串包含路由查询字符串中的所有静态参数，则请求匹配路由。查询中带有静态参数 param（任何 UTF-8 文本字符串）的路由只会匹配查询字符串中具有该确切路径段的请求。

```rust
#[get("/?hello&cat=♥")]
fn cats() -> &'static str {
    "Hello, kittens!"
}
```

##### 动态参数

`<param>` 的单个动态参数的作用与声明为 param 的表单字段相同。特别是，Rocket 将期望查询表单包含一个带有 key `param` 的字段，并将移位的字段推送到 `param` 类型。与表单一样，解析失败时使用默认值。下面的示例使用单个值 `name`、集合 `color`、嵌套表单 `person` 和默认为 `None` 的 `other` 值说明了这一点：

```rust
#[derive(Debug, PartialEq, FromFormField)]
enum Color {
    Red,
    Blue,
    Green
}

#[derive(Debug, PartialEq, FromForm)]
struct Pet<'r> {
  name: &'r str,
  age: usize,
}

#[derive(Debug, PartialEq, FromForm)]
struct Person<'r> {
  pet: Pet<'r>,
}

#[get("/?<name>&<color>&<person>&<other>")]
fn hello(name: &str, color: Vec<Color>, person: Person<'_>, other: Option<usize>) {
    assert_eq!(name, "George");
    assert_eq!(color, [Color::Red, Color::Green, Color::Green, Color::Blue]);
    assert_eq!(other, None);
    assert_eq!(person, Person {
      pet: Pet { name: "Fi Fo Alex", age: 1 }
    });
}

// A request with these query segments matches as above.
name=George&\
color=red&\
color=green&\
person.pet.name=Fi+Fo+Alex&\
color=green&\
person.pet.age=1&\
color=blue&\
extra=yes\
```

##### Trailing 参数

`<param..>` 的尾随动态参数收集所有与声明的静态或动态参数不匹配的查询段。换句话说，其他不匹配的段被推送，不移位，到 `<param..>` 类型：

```rust
use rocket::form::Form;

#[derive(FromForm)]
struct User<'r> {
    name: &'r str,
    active: bool,
}

#[get("/?hello&<id>&<user..>")]
fn user(id: usize, user: User<'_>) {
    assert_eq!(id, 1337);
    assert_eq!(user.name, "Bob Smith");
    assert_eq!(user.active, true);
}

// A request with these query segments matches as above.
hello&\
name=Bob+Smith&\
id=1337&\
active=yes\
```

### 错误捕获

捕获函数可以接受零个、一个或两个参数。如果 `catcher` 接受一个参数，它必须是 `&Request` 类型。它需要两个，它们必须按顺序是 `Status` 和 `&Request` 类型。与路由一样，返回类型必须实现 `Responder`。

```rust
#[catch(404)]
fn not_found(req: &Request) -> String {
    format!("Sorry, '{}' is not a valid path.", req.uri())
}
```

并且进行注册。

```rust
fn main() {
    rocket::build().register("/", catchers![not_found]);
}
```

##### 作用域

`register()` 的第一个参数是捕获函数范围的路径，称为捕获函数的基础。函数的基础决定了它将为哪些请求处理错误。具体来说，函数的基必须是错误请求的前缀才能被调用。当可以调用多个捕获函数时，具有最长基数的捕获函数优先。

```rust
#[catch(404)]
fn general_not_found() -> &'static str {
    "General 404"
}

#[catch(404)]
fn foo_not_found() -> &'static str {
    "Foo 404"
}

#[launch]
fn rocket() -> _ {
    rocket::build()
        .register("/", catchers![general_not_found])
        .register("/foo", catchers![foo_not_found])
}
```

##### 默认捕获函数

默认捕获函数是处理所有状态代码的捕获函数。如果没有为给定错误注册特定于状态的捕获函数，则将它们作为后备调用。使用 `#[catch(default)]` 声明默认函数，并且必须同样使用 `register()` 注册：

```rust
use rocket::Request;
use rocket::http::Status;

#[catch(default)]
fn default_catcher(status: Status, request: &Request) { /* .. */ }

#[launch]
fn rocket() -> _ {
    rocket::build().register("/", catchers![default_catcher])
}
```

##### 内置捕获函数

Rocket 提供了一个内置的默认函数。它根据 Accept 标头的值生成 HTML 或 JSON。因此，自定义捕获函数只需要注册自定义错误处理。