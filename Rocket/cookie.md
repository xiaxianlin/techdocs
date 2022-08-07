# Cookie

通过引入内置请求守卫 `CookieJar` 来对 cookie 进行操作。

```rust
use rocket::http::CookieJar;

#[get("/")]
fn index(cookies: &CookieJar<'_>) -> Option<String> {
    cookies.get("message").map(|crumb| format!("Message: {}", crumb.value()))
}
```

### 私有 cookie

通过 CookieJar::add() 方法添加的 Cookie 是明文设置的。换句话说，值集对客户端是可见的。对于敏感数据，Rocket 提供私有 cookie。私有 cookie 与常规 cookie 类似，不同之处在于它们使用经过身份验证的加密进行加密，这是一种同时提供机密性、完整性和真实性的加密形式。因此，客户无法检查、篡改或制造私人 cookie。如果您愿意，您可以将私有 cookie 视为经过签名和加密的。

需要开启特性才能使用。

```rust
## in Cargo.toml
rocket = { version = "0.5.0-rc.2", features = ["secrets"] }
```

开始之后就可以使用 `private cookie` 的 api 了，大部分方法都是以 `_private` 结尾的。主要有 `get_private` 、 `get_pending`、`add_private`、`remove_private` 四个方法。

```rust
cookies.get_private("user_id");
cookies.remove_private(Cookie::named("user_id"));
```

### 私密 key

为了加密私有 cookie，Rocket 使用在 secret_key 配置参数中指定的 256 位密钥。在调试模式下编译时，会自动生成一个新密钥。在发布模式下，如果启用了机密功能，Rocket 会要求您设置密钥。不这样做会导致在启动时出现硬错误。参数的值可以是 256 位 base64 或十六进制字符串或 32 字节切片。生成适合用作 secret_key 配置值的字符串通常通过 openssl 等工具完成。使用 openssl，可以使用命令 openssl rand -base64 32 生成 256 位 base64 密钥。
