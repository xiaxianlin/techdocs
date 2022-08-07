# Fairing(整流罩)

Fairings 是 Rocket 的结构化中间件方法。使用整流罩，程序可以连接到请求生命周期，以记录或重写有关传入请求和传出响应的信息。

任何实现了 `Fairing` 的结构都是 fairing(整流罩)。Fairings 与 Rocket 的请求生命周期挂钩，接收传入请求和传出响应等事件的回调。Rocket 将有关这些事件的信息传递给整流罩，整流罩可以根据信息做它想做的事情。这包括重写请求或响应、记录有关事件的信息或什么都不做。

##### 与其他框架的中间件不同之处

- Fairings 不能直接终止或响应传入的请求。
- Fairings 不能将任意的、非请求的数据注入到请求中。
- Fairings 可以阻止应用程序启动。
- Fairings 可以检查和修改应用程序的配置。

### Attach(附加)

整流罩通过 `Rocket` 实例上的 `attach` 方法注册到 `Rocket`。只有连接了整流罩时，它的回调才会触发。

```rust
#[launch]
fn rocket() -> _ {
    rocket::build().attach(req_fairing).attach(res_fairing)
}
```

整流罩按照它们附加的顺序执行：第一个附加的整流罩在所有其他整流罩之前执行其回调。整流罩可以连接任意次数。除了单例整流罩，所有附加的实例都在运行时轮询。整流罩回调可能不可交换，安装整流罩的顺序很重要。

### Callabck(回调)

Rocket 会针对 5 个事件发出整流罩回调。下面简要描述了这些事件中的每一个，并在 [`Fairing`](https://api.rocket.rs/v0.5-rc/rocket/fairing/trait.Fairing.html) trait 文档中进行了详细描述。

##### Ignite(`on_ignite`)

启动期间调用启动回调 启动回调可以任意修改正在构建的 Rocket 实例。它们通常用于解析和验证配置值、中止错误配置以及将解析后的值插入托管状态以供以后检索。

##### Liftoff(`on_liftoff`)

在 Rocket 应用程序启动后立即调用提升回调。 Liftoff 回调可以检查正在启动的 Rocket 实例。 Liftoff 回调可以是一个方便的钩子，用于启动与正在启动的 Rocket 应用程序相关的服务。

##### Request(`on_request`)

收到请求后立即调用请求回调。请求回调可以随意修改请求并查看传入的数据。但是可能不会中止或直接响应请求，通过请求保护或响应回调可以更好地处理这些问题。

##### Response(`on_response`)

当响应准备好发送到客户端时，将调用响应回调。响应回调可以修改部分或全部响应。因此，响应整流罩可用于在更大的应用程序失败时通过根据需要重写404响应来提供响应。作为另一个示例，响应整流罩也可用于将标头注入所有传出响应中。

##### Shutdown(`on_shutdown`)

触发关闭时调用关闭回调。此时，正常关机已经开始但尚未完成，不接受新请求，但应用程序可能仍在积极服务现有请求。所有注册的关闭整流罩同时运行，在恢复关闭之前等待所有整流罩的解决。

### 实现一个整流罩

Fairing 实现有一个必需的方法：info，它返回一个 Info 结构。Rocket 使用此结构为整流罩分配名称并确定整流罩正在注册的回调集。实现 `Fairing` 的类型必须是 `Send + Sync + 'static`。这意味着整流罩必须可以跨线程边界发送（发送）、线程安全（同步），并且只有静态引用，如果有的话（'static）。请注意，这些界限并不禁止 Fairing 保持状态：该状态只需要是线程安全的并且是静态可用的或堆分配的。

##### 示例

实现一个计算 `GET` 和 `POST` 请求数量的整流罩。

```rust
use std::io::Cursor;
use std::sync::atomic::{AtomicUsize, Ordering};

use rocket::{Request, Data, Response};
use rocket::fairing::{Fairing, Info, Kind};
use rocket::http::{Method, ContentType, Status};

struct Counter {
    get: AtomicUsize,
    post: AtomicUsize,
}

#[rocket::async_trait]
impl Fairing for Counter {
    // This is a request and response fairing named "GET/POST Counter".
    fn info(&self) -> Info {
        Info {
            name: "GET/POST Counter",
            kind: Kind::Request | Kind::Response
        }
    }

    // Increment the counter for `GET` and `POST` requests.
    async fn on_request(&self, request: &mut Request<'_>, _: &mut Data<'_>) {
        match request.method() {
            Method::Get => self.get.fetch_add(1, Ordering::Relaxed),
            Method::Post => self.post.fetch_add(1, Ordering::Relaxed),
            _ => return
        };
    }

    async fn on_response<'r>(&self, request: &'r Request<'_>, response: &mut Response<'r>) {
        // Don't change a successful user's response, ever.
        if response.status() != Status::NotFound {
            return
        }

        // Rewrite the response to return the current counts.
        if request.method() == Method::Get && request.uri().path() == "/counts" {
            let get_count = self.get.load(Ordering::Relaxed);
            let post_count = self.post.load(Ordering::Relaxed);
            let body = format!("Get: {}\nPost: {}", get_count, post_count);

            response.set_status(Status::Ok);
            response.set_header(ContentType::Plain);
            response.set_sized_body(body.len(), Cursor::new(body));
        }
    }
}
```

### Ad-Hoc Fairings

对于更简单的情况，实现 `Fairing` trait 可能很麻烦。这就是 Rocket 提供 AdHoc 类型的原因，它从一个简单的函数或闭包创建一个整流罩。使用 AdHoc 类型很容易：只需调用 AdHoc 上的 on_ignite、on_liftoff、on_request、on_response 或 on_shutdown 构造函数，即可从函数或闭包创建整流罩。

```rust
use rocket::fairing::AdHoc;
use rocket::http::Method;

rocket::build()
    .attach(AdHoc::on_liftoff("Liftoff Printer", |_| Box::pin(async move {
        println!("...annnddd we have liftoff!");
    })))
    .attach(AdHoc::on_request("Put Rewriter", |req, _| Box::pin(async move {
        req.set_method(Method::Put);
    })))
    .attach(AdHoc::on_shutdown("Shutdown Printer", |_| Box::pin(async move {
        println!("...shutdown has commenced!");
    })));
```

