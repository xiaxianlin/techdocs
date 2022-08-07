# 响应

请求的处理函数的返回类型似乎是任意的，可以返回实现 `Responder` 特征的任何类型的值，包括自己的值。

### 响应者（Responder）

实现 `Responder` 的类型知道如何从它们的值生成响应。响应包括 HTTP 状态、标头和正文。主体可以是固定大小的或流式传输的。给定的 `Responder` 实现决定使用哪个。例如，`String` 使用固定大小的主体，而 `File` 使用流式响应。Responders 可以根据他们正在响应的传入请求动态调整他们的响应。

##### 封装

Rocket 提供了封装另一个 Responder 的 Responder。大概是这样的形式：

```rust
struct WrappingResponder<R>(R);
```

其中 `R` 也是实现了 `Responder` 的类型，封装过后的 Responder 会处理响应后并返回 `R`，并让 `R` 继续处理响应。

比如可以修改状态的内置模块 `status`，可以在修改状态后继续返回其他值。

```rust
use rocket::response::status;

#[post("/<id>")]
fn new(id: usize) -> status::Accepted<String> {
    status::Accepted(Some(format!("id: '{}'", id)))
}
```

还是可以使用 `status::Custom` 来设置 `Content-Type`。

```rust
use rocket::http::Status;
use rocket::response::{content, status};

#[get("/")]
fn json() -> status::Custom<content::RawJson<&'static str>> {
    status::Custom(Status::ImATeapot, content::RawJson("{ \"hi\": \"world\" }"))
}
```

提供了内置的 Responder 如 `(Status, R)` 和 `(ContentType, R)`，其中 `R` 实现了 Responder，处理响应状态和响应内容类型。

```rust
use rocket::http::{Status, ContentType};

#[get("/")]
fn json() -> (Status, (ContentType, &'static str)) {
    (Status::ImATeapot, (ContentType::JSON, "{ \"hi\": \"world\" }"))
}
```

通过派生 Responder 来方便复用。

```rust
#[derive(Responder)]
#[response(status = 418, content_type = "json")]
struct RawTeapotJson(&'static str);

#[get("/")]
fn json() -> RawTeapotJson {
    RawTeapotJson("{ \"hi\": \"world\" }")
}
```

##### 状态

Status 生成的响应取决于状态码本身。对于错误状态代码（在 [400, 599] 范围内），Status 会转发到相应的错误捕获函数。

下表总结了 Status 为这些代码和其他代码生成的响应：

| 状态范围        | 响应方式                            |
| --------------- | ----------------------------------- |
| [400, 599]      | 发送给对应状态的错误处理函数        |
| 100, [200, 205] | 具有给定状态的空。                  |
| All others.     | 非法状态，全部发送给 `500` 处理函数 |

#### 自定义 Responder

`Responder` 有2个生命周期：`Responder<'r, 'o: 'r>`

- `'r` 约束引用 `&'r Request`
- `'o` 约束返回的 `Response<'o>` 与 `&'r Request` 周期一致。

```rust
// 响应不饱和借用数据
impl<'r> Responder<'r, 'static> for A {
    fn respond_to(self, _: &'r Request<'_>) -> response::Result<'static> {
        todo!()
    }
}

// 响应从请求借用数据
impl<'r> Responder<'r, 'r> for B<'r> {
    fn respond_to(self, _: &'r Request<'_>) -> response::Result<'r> {
        todo!()
    }
}

// 响应是或包装了可能超过请求的借用。
impl<'r, 'o: 'r> Responder<'r, 'o> for &'o C {
    fn respond_to(self, _: &'r Request<'_>) -> response::Result<'o> {
        todo!()
    }
}

// 响应封装了一个已存在 Responder
impl<'r, 'o: 'r, R: Responder<'r, 'o>> Responder<'r, 'o> for D<R> {
    fn respond_to(self, _: &'r Request<'_>) -> response::Result<'o> {
        todo!()
    }
}
```

##### 示例

先声明一个类型。

```rust
struct Person {
    name: String,
    age: u16
}
```

然后实现 `Responder`。设置两个标头字段：`X-Person-Name` 和 `X-Person-Age` 并在响应正文中提供对象的自定义表示 `（Content-Type：application/x-person）`。

```rust
use std::io::Cursor;

use rocket::request::Request;
use rocket::response::{self, Response, Responder};
use rocket::http::ContentType;

impl<'r> Responder<'r, 'static> for Person {
    fn respond_to(self, req: &'r Request<'_>) -> response::Result<'static> {
        let string = format!("{}:{}", self.name, self.age);
        Response::build_from(string.respond_to(req)?)
            .raw_header("X-Person-Name", self.name)
            .raw_header("X-Person-Age", self.age.to_string())
            .header(ContentType::new("application", "x-person"))
            .ok()
    }
}
```

最后使用 `Person` 作为 `Responder` 返回。

```rust
#[get("/person/<id>")]
fn person(id: usize) -> Option<Person> {
    Person::from_id(id)
}
```

还可以用另外一种形式实现同样的效果。

```rust
use rocket::http::Header;
use rocket::response::Responder;

#[derive(Responder)]
#[response(content_type = "application/x-person")]
struct Person {
    text: String,
    name: Header<'static>,
    age: Header<'static>,
}

impl Person {
    fn new(name: &str, age: usize) -> Person {
        Person {
            text: format!("{}:{}", name, age),
            name: Header::new("X-Person-Name", name.to_string()),
            age: Header::new("X-Person-Age", age.to_string())
        }
    }
}
```

### 实现 Responder 的标准库

##### Strings

`&str` 和 `String` 的 `Responder` 实现是直截了当的：字符串用作调整大小的主体，响应的 `Content-Type` 设置为 `text/plain`。具体实现代码如下：

```rust
#[rocket::async_trait]
impl<'r> Responder<'r, 'static> for String {
    fn respond_to(self, _: &'r Request<'_>) -> response::Result<'static> {
        Response::build()
            .header(ContentType::Plain)
            .sized_body(self.len(), Cursor::new(self))
            .ok()
    }
}
```

##### Option

`Option` 是一个封装的响应者： `Option<T>` 只能在 `T` 实现 `Responder` 时作为返回。如果 `Option` 是 `Some`，则使封装的响应者来响应客户端。否则，将向客户端返回 `404 - Not Found` 错误。当直到处理时才知道内容是否存在时，此实现使 `Option` 成为一种方便的返回类型。

```rust
use rocket::fs::NamedFile;

#[get("/<file..>")]
async fn files(file: PathBuf) -> Option<NamedFile> {
    NamedFile::open(Path::new("static/").join(file)).await.ok()
}
```

##### Result

`Result` 也是一个封装的响应者：`Result<T, E>` 只有在 `T` 实现 `Responder` 和 `E` 实现 `Responder` 时作为返回。响应者可以在运行时动态选择，并且可以根据情况使用两种不同类型的响应。

```rust
use rocket::fs::NamedFile;
use rocket::response::status::NotFound;

#[get("/<file..>")]
async fn files(file: PathBuf) -> Result<NamedFile, NotFound<String>> {
    let path = Path::new("static/").join(file);
    NamedFile::open(&path).await.map_err(|e| NotFound(e.to_string()))
}
```

### 内置 Responder

- [`NamedFile`](https://api.rocket.rs/v0.5-rc/rocket/fs/struct.NamedFile.html) - 将文件流式传输到客户端；根据文件的扩展名自动设置 Content-Type。
- [`Redirect`](https://api.rocket.rs/v0.5-rc/rocket/response/struct.Redirect.html) - 将客户端重定向到不同的 URI。
- [`content`](https://api.rocket.rs/v0.5-rc/rocket/response/content/) - 包含覆盖 Content-Type 响应的类型。
- [`status`](https://api.rocket.rs/v0.5-rc/rocket/response/status/) - 包含覆盖响应状态代码的类型。
- [`Flash`](https://api.rocket.rs/v0.5-rc/rocket/response/struct.Flash.html) - 设置访问时删除的 “flash” cookie。
- [`Json`](https://api.rocket.rs/v0.5-rc/rocket/serde/json/struct.Json.html) - 自动将值序列化为 JSON。
- [`MsgPack`](https://api.rocket.rs/v0.5-rc/rocket/serde/msgpack/struct.MsgPack.html) - 自动将值序列化到 MessagePack 中。
- [`Template`](https://api.rocket.rs/v0.5-rc/rocket_dyn_templates/struct.Template.html) - 使用把手或 Tera 渲染动态模板。

##### 异步流

流响应器允许服务潜在的无限异步流。可以从任何异步 Stream 或 AsyncRead 类型创建流，或者通过使用流的生成器语法创建 `stream!` 宏及其类型等效项。流是单向实时通信的构建块。

```rust
use std::io;
use std::net::SocketAddr;

use rocket::tokio::net::TcpStream;
use rocket::response::stream::ReaderStream;

#[get("/stream")]
async fn stream() -> io::Result<ReaderStream![TcpStream]> {
    let addr = SocketAddr::from(([127, 0, 0, 1], 9999));
    let stream = TcpStream::connect(addr).await?;
    Ok(ReaderStream::one(stream))
}
```

使用生成器语法创建一个无限文本流 `TextStream` 。

```rust
use rocket::tokio::time::{Duration, interval};
use rocket::response::stream::TextStream;

#[get("/infinite-hellos")]
fn hello() -> TextStream![&'static str] {
    TextStream! {
        let mut interval = interval(Duration::from_secs(1));
        loop {
            yield "hello";
            interval.tick().await;
        }
    }
}
```

