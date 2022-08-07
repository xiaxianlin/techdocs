# 数据守卫

数据保护是对请求的正文数据进行操作的保护。数据守卫通过 FromData 的实现来验证和解析请求主体数据。换句话说，如果一个类型实现了 FromData，它就是一个数据保护。

数据守卫是数据路由属性参数的目标：

```rust
#[post("/submit", data = "<var>")]
fn submit(var: DataGuard) { /* ... */ }
```

一条路由最多可以有一个数据保护。在上面的代码中 `var` 用作数据保护类型 DataGuard 的参数名称。当提交路由匹配时，Rocket 将调用 T 类型的 FromData 实现。只有当守卫成功返回时才会调用处理程序。

### Trait

FromData 是一个异步特征。 FromData 的实现必须用#[rocket::async_trait] 的属性修饰：

```rust
use rocket::request::Request;
use rocket::data::{self, Data, FromData};

#[rocket::async_trait]
impl<'r> FromData<'r> for MyType {
    type Error = MyError;

    async fn from_data(req: &'r Request<'_>, data: Data<'r>) -> data::Outcome<'r, Self> {
        /* .. */
    }
}
```

### 示例

首先定一个 `Person` 类型 和 `Error` 类型。并在 Person 类型上实现 `Serialize` 和 `Deserialize`，方便后面使用。

```rust
#[derive(Debug, Serialize, Deserialize)]
pub struct Person<'r> {
    name: &'r str,
    age: u16,
}

#[derive(Debug)]
pub enum Error {
    TooLarge,
    NoColon,
    InvalidAge,
    Io(std::io::Error),
}
```

然后再为 Person 实现 FromData。

```rust
#[rocket::async_trait]
impl<'r> FromData<'r> for Person<'r> {
    type Error = Error;

    async fn from_data(req: &'r Request<'_>, data: Data<'r>) -> data::Outcome<'r, Self> {
        use rocket::outcome::Outcome::*;
        use Error::*;
		// 获取请求的 contentType 进行对比，如果不是指定的，则透传下去
        let person_ct = ContentType::new("application", "x-person");
        if req.content_type() != Some(&person_ct) {
            return Forward(data);
        }
		// 读取数据
        let limit = req.limits().get("person").unwrap_or(256.bytes());
        let string = match data.open(limit).into_string().await {
            Ok(string) if string.is_complete() => string.into_inner(),
            Ok(_) => return Failure((Status::PayloadTooLarge, TooLarge)),
            Err(e) => return Failure((Status::BadRequest, Io(e))),
        };
		// 数据本地缓存
        let string = request::local_cache!(req, string);
		// 解析数据
        let (name, age) = match string.find(':') {
            Some(i) => (&string[..i], &string[(i + 1)..]),
            None => return Failure((Status::BadRequest, NoColon)),
        };
        let age: u16 = match age.parse() {
            Ok(age) => age,
            Err(_) => return Failure((Status::BadRequest, InvalidAge)),
        };
        Success(Person { name, age })
    }
}
```

定义路由。

```rust
#[post("/person", data = "<person>")]
pub fn person(person: Person<'_>) -> String {
    format!("match application/x-person")
}

#[post("/person", data = "<person>", rank = 2)]
pub fn person2(person: Result<Person<'_>, Error>) -> String {
    format!("match Error Person !")
}

#[post("/person", data = "<person>", rank = 3)]
pub fn person3(person: Option<Person<'_>>) -> String {
    format!("match None Person !")
}

#[post("/person", data = "<person>", rank = 4)]
pub fn person4(person: Json<Person<'_>>) -> &str {
    "match Json person"
}
```

这个路由配置会出现以下情况：

- 当 contentType = application/x-person 时
    - 解析成功：会调用 `person` 函数并返回字符串 `"match application/x-person"`
    - 解析失败：会直接返回 400 的错误
- 当 contentType != application/x-person 时
    - 会依次调用 `person`、`person2` 和 `person3`，但是不会调用 `person4` ，应该在 3 的时候就匹配到 `None`。

如果交换 3 和 4 的顺序，并且 contentType = application/json 时，会命中 `person4`。

```rust
#[post("/person", data = "<person>", rank = 4)]
pub fn person3(person: Option<Person<'_>>) -> String {
    format!("match None Person !")
}

#[post("/person", data = "<person>", rank = 3)]
pub fn person4(person: Json<Person<'_>>) -> &str {
    "match Json person"
}
```

如果提交的是 json 数据，因为实现了序列化和反序列化，就会解析 Person 类型的数据。
