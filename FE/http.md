# HTTP
> 超文本传输协议（HTTP）是一个用于传输超媒体文档（例如 HTML）的应用层协议。它是为 Web 浏览器与 Web 服务器之间的通信而设计的，但也可以用于其他目的。HTTP 遵循经典的客户端-服务端模型，客户端打开一个连接以发出请求，然后等待直到收到服务器端响应。HTTP 是无状态协议，这意味着服务器不会在两个请求之间保留任何数据（状态）。尽管通常基于 TCP/IP 层，但它可以在任何可靠的传输层上使用，也就是说，该协议不会像 UDP 那样静默的丢失消息。RUDP——作为 UDP 的可靠化升级版本——是一种合适的替代选择。

### 基本性质
- 简单的
- 可扩展的
- 无状态，有会话的

### 流
- 打开一个TCP连接
- 发送一个HTTP报文
- 读取服务端返回的报文
- 关闭连接或者为后续请求重用连接

### 缓存
> 缓存是一种保存资源副本并在下次请求时直接使用该副本的技术。当 web 缓存发现请求的资源已经被存储，它会拦截请求，返回该资源的拷贝，而不会去源服务器重新下载。这样带来的好处有：缓解服务器端压力，提升性能(获取资源的耗时更短了)。对于网站来说，缓存是达到高性能的重要组成部分。缓存需要合理配置，因为并不是所有资源都是永久不变的：重要的是对一个资源的缓存应截止到其下一次发生改变（即不能缓存过期的资源）。

> 缓存的种类有很多,其大致可归为两类：私有与共享缓存。共享缓存存储的响应能够被多个用户使用。私有缓存只能用于单独用户。本文将主要介绍浏览器与代理缓存，除此之外还有网关缓存、CDN、反向代理缓存和负载均衡器等部署在服务器上的缓存方式，为站点和 web 应用提供更好的稳定性、性能和扩展性。

- 缓存只能存在GET响应
- 本地缓存
	- 缓存控制头Cache-Control:<no-store | no-cache | private | public | max-age | must-revalidate>
	-  pragma跟Cache-Control作用一直，用于兼容1.0版本
- 对比缓存
	- expires是过期时间，兼容1.0版本
	- ETag是缓存的一种强校验器，If-None-Match来验证缓存
	- Last-Modified是缓存的一种弱校验器，If-Modified-Since来验证缓存
- Vary HTTP 响应头决定了对于后续的请求头，如何判断是请求一个新的资源还是使用缓存的文件

### Cookie
> HTTP Cookie（也叫 Web Cookie 或浏览器 Cookie）是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。通常，它用于告知服务端两个请求是否来自同一浏览器，如保持用户的登录状态。Cookie 使基于无状态的HTTP协议记录稳定的状态信息成为了可能。

**主要作用**
- 会话状态管理
- 个性化设置
- 浏览器行为跟踪

- 响应头Set-Cookie创建cookie
	- Expires设置生命周期
	- Secure要求cookie只能通过被HTTPS协议加密过的请求发送给服务端
	- HttpOnly标识cookie对JS不可用
	- Domain和Path标识作用域
	- SameSite允许服务器要求某个 cookie 在跨站请求时不会被发送
- 请求头Cookie发送cookie

### 跨源资源共享（CORS）
> 跨源资源共享 (CORS) （或通俗地译为跨域资源共享）是一种基于HTTP 头的机制，该机制通过允许服务器标示除了它自己以外的其它origin（域，协议和端口），这样浏览器可以访问加载这些资源。跨源资源共享还通过一种机制来检查服务器是否会允许要发送的真实请求，该机制通过浏览器发起一个到服务器托管的跨源资源的"预检"请求。在预检中，浏览器发送的头中标示有HTTP方法和真实请求中会用到的头。

- 简单请求不会触发CORS预检请求
- Access-Control-Allow-Origin指定允许访问该资源的外域URI
- Access-Control-Expose-Headers 让服务器把允许浏览器访问的头放入白名单
- Access-Control-Max-Age 指定了preflight请求的结果能够被缓存多久
- Access-Control-Allow-Credentials 指定了当浏览器的credentials设置为true时是否允许浏览器读取response的内容
- Access-Control-Allow-Methods 用于预检请求的响应，其指明了实际请求所允许使用的 HTTP 方法
- Access-Control-Allow-Headers 用于预检请求的响应，其指明了实际请求中允许携带的首部字段

- Origin 表明预检请求或实际请求的源站。
- Access-Control-Request-Method 用于预检请求，将实际请求所使用的 HTTP 方法告诉服务器
- Access-Control-Request-Headers 首部字段用于预检请求，将实际请求所携带的首部字段告诉服务器

**解决方案**
- 服务端设置Access-Control-Allow-Origin
- 服务器做请求代理
- GET请求可以使用JSONP格式返回
- 主域与子域不同可以设置document.domain
- 使用postMessage进行跨域通信

### 消息
> HTTP消息是服务器和客户端之间交换数据的方式。有两种类型的消息︰ 请求（requests）--由客户端发送用来触发一个服务器上的动作；响应（responses）--来自服务器的应答。

**结构**
1. 一行起始行用于描述要执行的请求，或者是对应的状态，成功或失败。这个起始行总是单行的。
2. 一个可选的HTTP头集合指明请求或描述消息正文。
3. 一个空行指示所有关于请求的元数据已经发送完毕。
4. 一个可选的包含请求相关数据的正文 (比如HTML表单内容), 或者响应相关的文档。 正文的大小有起始行的HTTP头来指定。

**HTTP/2 帧**
- HTTP/1.X缺陷
	- Header 不像 body，它不会被压缩。
	* 两个报文之间的 header 通常非常相似，但它们仍然在连接中重复传输。
	* 无法复用。当在同一个服务器打开几个连接时：TCP 热连接比冷连接更加有效。
* 将 HTTP/1.x 消息分成帧并嵌入到流 (stream) 中。数据帧和报头帧分离，这将允许报头压缩。将多个流组合，这是一个被称为 多路复用 (multiplexing) 的过程，它允许更有效的底层 TCP 连接。

### 连接管理
- 短链接：每一个 HTTP 请求都由它自己独立的连接完成；这意味着发起每一个 HTTP 请求之前都会有一次 TCP 握手，而且是连续不断的
- 长连接：连接会保持一段时间，重复用于发送一系列请求，节省了新建 TCP 连接握手的时间，还可以利用 TCP 的性能增强能力
	- 连接在空闲一段时间后会被关闭，使用Keep-Alive来指定连接保持时间
	- 空闲状态会消耗服务器资源
	- 在重负载时，可能遭受DoS攻击
- 流水线：在同一条长连接上发出连续的请求，而不用等待应答返回
- 域名切片：为每个域名建立多个连接，以实现并发请求

### HTTP/2
> HTTP/2 的主要目标是通过支持完整的请求与响应复用来减少延迟，通过有效压缩 HTTP 标头字段将协议开销降至最低，同时增加对请求优先级和服务器推送的支持。 为达成这些目标，HTTP/2 还给我们带来了大量其他协议层面的辅助实现，例如新的流控制、错误处理和升级机制。

* 所有通信都在一个 TCP 连接上完成，此连接可以承载任意数量的双向数据流。
* 每个数据流都有一个唯一的标识符和可选的优先级信息，用于承载双向消息。
* 每条消息都是一条逻辑 HTTP 消息（例如请求或响应），包含一个或多个帧。
* 帧是最小的通信单位，承载着特定类型的数据，例如 HTTP 标头、消息负载等等。 来自不同数据流的帧可以交错发送，然后再根据每个帧头的数据流标识符重新组装。

- 二级制分帧层
	- HTTP/2 所有性能增强的核心在于新的二进制分帧层，它定义了如何封装 HTTP 消息并在客户端与服务器之间传输。
- 数据流、消息与帧
	-  数据流: 已建立的连接内的双向字节流，可以承载一条或多条消息。
	* 消息: 与逻辑请求或响应消息对应的完整的一系列帧。
	* 帧: HTTP/2 通信的最小单位，每个帧都包含帧头，至少也会标识出当前帧所属的数据流。
- 请求与响应复用
	- 客户端和服务器可以将 HTTP 消息分解为互不依赖的帧，然后交错发送，最后再在另一端把它们重新组装起来。
	- HTTP/2 中的新二进制分帧层解决了 HTTP/1.x 中存在的队首阻塞问题，也消除了并行处理和发送请求及响应时对多个连接的依赖。 结果，应用速度更快、开发更简单、部署成本更低。
	- 并行交错地发送多个请求，请求之间互不影响。
	* 并行交错地发送多个响应，响应之间互不干扰。
	* 使用一个连接并行发送多个请求和响应。
	* 不必再为绕过 HTTP/1.x 限制而做很多工作
	
	- 消除不必要的延迟和提高现有网络容量的利用率，从而减少页面加载时间。
- 数据流优先级
	- 将 HTTP 消息分解为很多独立的帧之后，复用多个数据流中的帧，客户端和服务器交错发送和传输这些帧的顺序就成为关键的性能决定因素。
	- 允许每个数据流都有一个关联的权重和依赖关系
		- 可以向每个数据流分配一个介于 1 至 256 之间的整数
		- 每个数据流与其他数据流之间可以存在显式依赖关系
-  每个来源一个连接
	- 不再依赖多个 TCP 连接去并行复用数据流
	- 连接都是永久的，而且仅需要每个来源一个连接
- 流控制
	- 一种阻止发送方向接收方发送大量数据的机制
	- 流控制具有方向性。 每个接收方都可以根据自身需要选择为每个数据流和整个连接设置任意的窗口大小
	- 流控制基于信用。 每个接收方都可以公布其初始连接和数据流流控制窗口（以字节为单位），每当发送方发出 DATA 帧时都会减小，在接收方发出 WINDOW_UPDATE 帧时增大
	- 流控制无法停用。 建立 HTTP/2 连接后，客户端将与服务器交换 SETTINGS 帧，这会在两个方向上设置流控制窗口。 流控制窗口的默认值设为 65,535 字节，但是接收方可以设置一个较大的最大窗口大小（2^31-1 字节），并在接收到任意数据时通过发送 WINDOW_UPDATE 帧来维持这一大小
	- 流控制为逐跃点控制，而非端到端控制。 即，可信中介可以使用它来控制资源使用，以及基于自身条件和启发式算法实现资源分配机制
- 服务器推送
	- 服务器可以对一个客户端请求发送多个响应
	- 所有服务器推送数据流都由 PUSH_PROMISE 帧发起
	- 客户端接收到 PUSH_PROMISE 帧后，它可以根据自身情况选择拒绝数据流（通过 RST_STREAM 帧）
	- 客户端可以限制并行推送的数据流数量；调整初始的流控制窗口以控制在数据流首次打开时推送的数据量；或完全停用服务器推送
- 标头压缩
	- 为了减少此开销和提升性能，HTTP/2 使用 HPACK 压缩格式压缩请求和响应标头元数据
	- 支持通过静态霍夫曼代码对传输的标头字段进行编码，从而减小了各个传输的大小
	- 要求客户端和服务器同时维护和更新一个包含之前见过的标头字段的索引列表，此列表随后会用作参考，对之前传输的值进行有效编码


### 状态码
- 信息响应
	- 100 - Continue
	- 101 - Switching Protocol
	- 102 - Processing
	- 103 - Early Hints
- 成功响应
	- 200 - OK
	- 201 - Created
	- 202 - Accepted
	- 203 - Non-Authoritative Infomation
	- 204 - No Content
	- 205 - Reset Content
	- 206 - Partial Content
	- 207 - Multi-Status
	- 208 - Already Reported
	- 226 - IM Used
- 重定向
	- 300 - Multiple Choice
	- 301 - Moved Permanently
	- 302 - Found
	- 303 - See Other
	- 304 - Not Modified
	- 306 - unused
	- 307 - Temporary Redirect
	- 308 - Permanent Redirect
- 客户端响应
	- 400 - Bad Request
	- 401 - Unauthorized
	- 402 - Payment Required
	- 403 - Forbidden
	- 404 - Not Found
	- 405 - Method Not Allow
	- 406 - Not Accepatable
	- 407 - Proxy Authentication Required
	- 408 - Request Timeout
	- 409 - Confilict
	- 410 - Gone
	- 411 - Length Required
	- 412 - Precondition Failed
	- 413 - Payload Too Large
	- 414 - URI Too Long
	- 415 - Unsupported Media Type
	- 416 - Range Not Satisfiable
	- 417 - Expectation Failed
	- 418 - I’m a teapot
	- 421 - Misdirected Request
	- 422 - Unprocessable Entity
	- 423 - Locked
	- 424 - Failed Dependency
	- 425 - Too Early
	- 426 - Upgrade Required
	- 428 - Precondition Required
	- 429 - Too Many Requests
	- 431 - Request Header Fields Too Large
	- 451 - Unavailable For Legal Reasons
- 服务端响应
	- 500 - Internal Server Error
	- 501 - Not Implemented
	- 502 - Bad Gateway
	- 503 - Service Unavailable
	- 504 - Gateway Timeout
	- 505 - HTTP Version Not Supported
	- 506 - Variant Also Negotiates
	- 507 - Insufficient Storage
	- 508 - Loop Detected
	- 510 - Not Extended
	- 511 - Network Authentication Required
