# 请求守卫

请求守卫是一种表示任意验证策略的类型。验证策略通过`FromRequest`实现。换句话说，实现`FromRequest`的每个类型都是请求保护。实现了请求守卫的类型可以作为处理函数的参数存在，并且可以是任意数量的守卫，只有在所有守卫通过时才会调用处理函数发送。

##### 实现一个请求守卫

首先定一个守卫类型，和异常类型。

```rust
struct ApiKey<'r>(&'r str);

#[derive(Debug)]
enum ApiKeyError {
    Missing,
    Invalid,
}
```

实现 `FromRequest` ，`FromRequest`是一种*异步*特征。`FromRequest`的实现必须装饰为`#[rocket::async_trait]`的属性。

```rust
use rocket::http::Status;
use rocket::request::{self, Request, FromRequest};
#[rocket::async_trait]
impl<'r> FromRequest<'r> for ApiKey<'r> {
    type Error = ApiKeyError;

    async fn from_request(req: &'r Request<'_>) -> Outcome<Self, Self::Error> {
        /// Returns true if `key` is a valid API key string.
        fn is_valid(key: &str) -> bool {
            key == "valid_api_key"
        }

        match req.headers().get_one("x-api-key") {
            None => Outcome::Failure((Status::BadRequest, ApiKeyError::Missing)),
            Some(key) if is_valid(key) => Outcome::Success(ApiKey(key)),
            Some(_) => Outcome::Failure((Status::BadRequest, ApiKeyError::Invalid)),
        }
    }
}
```

最后给路由添加请求守卫。

```rust
#[get("/sensitive")]
fn sensitive(key: ApiKey<'_>) -> &'static str {
    "Sensitive data."
}
```

### 请求守卫透传

一般在多级权限，不同级别的访问需要不同处理方式，这个时候就需要进行请求守卫透传。比如我们对 `/dashboard` 进行访问的会根据不同类型的用户返回不同的信息。

要实现透传必须使用 `Request.guard::<T>()` 来获取其他请求守卫的透传的数据。T 必须也是请求守卫。

##### 实现一个透传例子

首先定一个数据库请求守卫，提供一个 `get_user()` 方法来获取用户信息，使用 `Outcome::Success()` 来返回请求守卫的数据。

```rust
use rocket::request::Outcome;

struct Database {}

impl Database {
    fn get_user(&mut self, _id: i32) -> Result<User, ()> {
        Ok(User { is_admin: false })
    }
}

#[rocket::async_trait]
impl<'r> FromRequest<'r> for Database {
    type Error = ();

    async fn from_request(_req: &'r Request<'_>) -> Outcome<Self, Self::Error> {
        Outcome::Success(Database {})
    }
}
```

然后实现一个普通用户的请求守卫。提供字段 `is_admin` 来判断是否是管理员，当拿到 user 数据后，调用 `or_forward()` 方法表示会进行透传，并传入透传数据。类似使用了 `Outcome::Forward()` 方法。

```rust
use rocket::outcome::IntoOutcome;

#[derive(Debug)]
struct User {
    is_admin: bool,
}

#[rocket::async_trait]
impl<'r> FromRequest<'r> for User {
    type Error = ();

    async fn from_request(request: &'r Request<'_>) -> Outcome<User, ()> {
        println!("User Request Guard");
        let mut db = try_outcome!(request.guard::<Database>().await);
        request
            .cookies()
            .get_private("user_id")
            .and_then(|cookie| cookie.value().parse().ok())
            .and_then(|id| db.get_user(id).ok())
            .or_forward(())
    }
}
```

使用宏 `try_outcome!` 配合 `Request.guard::<T>()` 可以获取到其他请求守卫的 `Success` 的数据。

这里需要注意的是使用 `or_forward()` 方法的话必须导入 `use rocket::outcome::IntoOutcome`。

接着实现一个管理员的请求守卫。

```rust
#[derive(Debug)]
struct Admin {
    user: User,
}

#[rocket::async_trait]
impl<'r> FromRequest<'r> for Admin {
    type Error = ();

    async fn from_request(request: &'r Request<'_>) -> Outcome<Admin, ()> {
        println!("Admin Request Guard");
        let user = try_outcome!(request.guard::<User>().await);
        if user.is_admin {
            Outcome::Success(Admin { user })
        } else {
            Outcome::Forward(())
        }
    }
}
```

通过判断是否是管理员来判断是否在这里停止还是继续透传。这里就是使用了 `Outcome::Forward()` 来进行透传。

最后绑定去定义路由。

```rust
#[get("/dashboard")]
fn admin_dashboard(admin: Admin) { }

#[get("/dashboard", rank = 2)]
fn user_dashboard(user: User) { }
```

只需要在路由的处理函数声明请求守卫类型的参数就表示使用请求守卫，参数是请求守卫调用 `Outcome::Forward()` 返回的值。

`rank` 参数决定了透传顺序。

### 本地状态

当非管理员用户登录时，数据库将被查询两次：一次是通过调用`User`保护的`Admin`守卫，第二次直接通过`User`保护。对于此类情况，应使用请求本地状态，如下所示：

```rust
#[rocket::async_trait]
impl<'r> FromRequest<'r> for &'r User {
    type Error = std::convert::Infallible;

    async fn from_request(request: &'r Request<'_>) -> Outcome<Self, Self::Error> {
        let user_result = request.local_cache_async(async {
            let db = request.guard::<Database>().await.succeeded()?;
            request.cookies()
                .get_private("user_id")
                .and_then(|cookie| cookie.value().parse().ok())
                .and_then(|id| db.get_user(id).ok())
        }).await;

        user_result.as_ref().or_forward(())
    }
}

#[rocket::async_trait]
impl<'r> FromRequest<'r> for Admin<'r> {
    type Error = std::convert::Infallible;

    async fn from_request(request: &'r Request<'_>) -> Outcome<Self, Self::Error> {
        let user = try_outcome!(request.guard::<&User>().await);
        if user.is_admin {
            Outcome::Success(Admin { user })
        } else {
            Outcome::Forward(())
        }
    }
}
```

需要注意的时这些请求守卫提供对*借用*数据（`&'a User`和`Admin<'a>`）的访问权限，因为这些数据现在归请求的缓存所有。
