# 路由

一个完整的路由两部分组成：

- 一组用于匹配传入请求的参数。
- 处理请求并返回响应的处理程序。

处理程序只是一个函数，它接受任意数量的参数并返回任意类型。

### 定义路由

使用属性宏生成一个路由。

```rust
#[route(GET, uri = "/")]
fn index() -> &' static str{
    "Hello, world"
}
```

7个方法路由宏：get、put、post、delete、head、options、patch。

```rust
#[get("/")]
fn index() -> &' static str{
    "Hello, world"
}
```

### 动态路径

##### 路径参数

在匹配路径上使用语法 `<param>` 定义一个参数，通过处理函数的入参进行访问，路径的参数名和函数的参数名需一致。

```rust
#[get("/hello/<name>")]
fn hello(name: &str) -> String {
    format!("Hello, {}!", name)
}
```

同时也可以定义多个参数。

```rust
#[get("/hello/<name>/<age>/<cool>")]
fn hello(name: &str, age: u8, cool: bool) -> String {
    if cool {
        format!("You're a cool {} year old, {}!", age, name)
    } else {
        format!("{}, we need to talk about your coolness.", name)
    }
}
```

使用 `<param..>` 进行多段匹配，剩余层级都可以匹配上。

```rust
use std::path::PathBuf;

#[get("/page/<path..>")]
fn get_page(path: PathBuf) { /* ... */ }

// 比如 /page/web/a.html ， path 值就是 web/a.html。
```

使用 `<_>` 忽略中间段。

```rust
#[get("/foo/<_>/bar")]
fn foo_bar() -> &'static str {
    "Foo _____ bar!"
}
// 以下路由均可匹配上：
// /foo/aaa/bar
// /foo/a/a/a/bar
// 但是 /foo/bar 是匹配不了的
```

通过结合多段匹配和忽略中间段 `<_..>` 实现匹配所有请求。

```rust
#[get("/<_..>")]
fn everything() -> &'static str {
    "Hey, you're here."
}
```

### 路径透传

##### 根据参数类型透传

当一个路径参数可以匹配多种类型的时候，可以进行路径透传，类似重载功能。

```rust
#[get("/user/<id>")]
fn user(id: usize) { /* ... */ }

#[get("/user/<id>", rank = 2)]
fn user_int(id: isize) { /* ... */ }

#[get("/user/<id>", rank = 3)]
fn user_str(id: &str) { /* ... */ }

#[launch]
fn rocket() -> _ {
    rocket::build().mount("/", routes![user, user_int, user_str])
}
```

把需要透传的路由处理函数都挂载到一个路由里面。通过 rank 来实现透传路由的函数调用顺序。如果没有明确指定 rank，Rocket 会分配一个默认值，默认值在 -12 到 -1 之间。在路径和查询中，默认排名优先于静态段而不是动态段：路由的路径和查询越静态，其优先级越高。

##### 根据请求守卫透传

当一个路径需要不同的请求守卫处理的时候，可以进行请求守卫透传。

```rust
use rocket::response::Redirect;

#[get("/login")]
fn login() -> Template { /* .. */ }

#[get("/admin")]
fn admin_panel(admin: AdminUser) -> &'static str {
    "Hello, administrator. This is the admin panel!"
}

#[get("/admin", rank = 2)]
fn admin_panel_user(user: User) -> &'static str {
    "Sorry, you must be an administrator to access this page."
}

#[get("/admin", rank = 3)]
fn admin_panel_redirect() -> Redirect {
    Redirect::to(uri!(login))
}
```

和参数类型类似，都是使用 `rank` 来定义透传的跳用顺序，与参数类型透传不一样的时候，请求守卫透传可以人工控制是否继续透传。

##### 根据数据类型透传

```rust
#[post("/person", data = "<person>")]
fn person(person: Person<'_>) { /* .. */ }

#[post("/person", data = "<person>", rank = 2)]
fn person2(person: Result<Person<'_>, Error>) { /* .. */ }

#[post("/person", data = "<person>", rank = 3)]
fn person3(person: Option<Person<'_>>) { /* .. */ }

#[post("/person", data = "<person>", rank = 4)]
fn person4(person: Person<'_>) -> &str {
    // Note that this is only possible because the data in `person` live
    // as long as the request through request-local cache.
    person.name
}
```

##### 注意事项

所有的透传都必须指定 rank 参数，否则就会抛出这个异常：

```rust
Error: Rocket failed to launch due to the following route collisions:
```

### URI

Rocket 的 `uri！`宏允许您健壮、类型安全和 URI 安全的方式在应用程序中构建路由的 URI。类型或路由参数不匹配在编译时被捕获，并且路由 URI 的更改会自动反映在生成的 URI 中。`uri!` 宏返回一个 `Origin` 结构，其中提供的路由的 URI 插入了给定的值。传入 `uri!` 的每个值使用值类型的 `UriDisplay` 实现在 URI 中的适当位置呈现。 `UriDisplay` 实现确保呈现的值是 URI 安全的。需要注意的是，`Origin` 实现了 `Into<Uri>`（并且通过扩展，`TryInto<Uri>`），因此可以根据需要使用 `.into()` 将其转换为 Uri，并传递给 `Redirect::to()` 等方法。

```rust
#[get("/<id>/<name>?<age>")]
fn person(id: Option<usize>, name: &str, age: Option<u8>) { /* .. */ }

uri!(person(101, "Mike Smith", Some(28))); // "/101/Mike%20Smith?age=28"
uri!(person(name = "Mike", id = 101, age = Some(28))); // "/101/Mike?age=28"
uri!(person(id = 101, age = Some(28), name = "Mike")); // "/101/Mike?age=28"
uri!("/api", person(id = 101, name = "Mike", age = Some(28))); // "/api/101/Mike?age=28"
uri!(person(101, "Mike", _)); // "/101/Mike"
uri!(person(id = 101, name = "Mike", age = _)); // "/101/Mike"
```

##### 派生 UriDisplay

可以为自定义类型派生 `UriDisplay`。对于出现在 URI 路径部分的类型，使用 `UriDisplayPath` 派生；对于出现在 URI 查询部分的类型，使用 `UriDisplayQuery` 派生。

```rust
use rocket::form::Form;

#[derive(FromForm, UriDisplayQuery)]
struct UserDetails<'r> {
    age: Option<usize>,
    nickname: &'r str,
}

#[post("/user/<id>?<details..>")]
fn add_user(id: usize, details: UserDetails) { /* .. */ }

uri!(add_user(120, UserDetails { age: Some(20), nickname: "Bob".into() })); // "/user/120?age=20&nickname=Bob"
```

##### 路由跳转

```rust
#[get("/admin")]
fn admin_panel_redirect() -> Redirect {
    Redirect::to(uri!(login))
}
```
