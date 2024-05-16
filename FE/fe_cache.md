# 前端的缓存

## HTTP缓存

通过网路请求资源或数据，尤其是数据，需要经过服务端一系列业务逻辑计算，往往速度较慢而且代价比较昂贵，客户端到服务端不断的请求与响应也非常消耗时间，这对用户体验来说也不是很友好。静态资源和非实时数据也不需要每次请求都需要服务端去处理，这时候就需要HTTP缓存来处理，HTTP缓存的缓存策略是由服务器决定，具体实现要交给代理服务器来实现，诸如Nginx、Apache之类的代理服务器，通过设置HTTP响应头部里面的Cache-Control来告诉客户端具体的策略详情，客户端也可以在请求头里面提供Cache-Control来告诉服务器自己希望的缓存策略。最终的存储还是发生客户端的。

#### ETag

在请求资源时，服务器会对资源生成一个验证token，并通过响应头里面的ETag返回给客户端，客户端拿到后放到请求头里面的if-None-Match，下次请求时就带上去给服务器校验，如果匹配上则返回状态304，并且返回服务器的缓存资源。

```
响应头
Cache-Control:max-age=60
Connection:keep-alive
Date:Fri, 29 Jun 2018 02:59:22 GMT
ETag:"5b359b87-21"
Last-Modified:Fri, 29 Jun 2018 02:37:59 GMT
Server:nginx/1.12.2
```

```
请求头
Accept:text/css,*/*;q=0.1
Accept-Encoding:gzip, deflate
Accept-Language:zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Cache-Control:max-age=0
Connection:keep-alive
Host:xxl.lo
If-Modified-Since:Fri, 29 Jun 2018 02:37:59 GMT
If-None-Match:"5b359b87-21"
Referer:http://xxl.lo/
User-Agent:Mozilla/5.0 (Macintosh; Intel …) Gecko/20100101 Firefox/60.0
```

#### Cache-Control

- no-cache: 浏览器在请求资源时，服务器会根据ETag来判断资源是否更新，如果没有更新则告诉浏览器使用缓存。这种情况一般用在静态资源。
- no-store: 服务器表示禁止浏览器以及中间缓存对资源进行存储。这种情况一般用在业务数据请求。
- public: 如果资源被打上了public标签，这个资源就会被缓存，而且谁都可以访问。一般使用max-age代替这个属性。
- private: 顾名思义，这是私有资源的缓存策略，中间缓存是不允许缓存这个资源。例如：用户浏览器可以换成HTML页面，但CDN不行。
- max-age: 过期时间，形式”max-age=60”，单位是秒。

#### 服务器校验策略

![](blob:tauri://localhost/7acb7491-046f-4bb1-9d48-91344a9dbf4b)

使用缓存的一些建议（来自图片出处的那篇文章）

- 一个资源只有唯一的URL，防止多次缓存。
- 确定服务器提供了校验token（ETag）。
- 分辨出不同资源在不同的媒介上缓存，比如CDN。
- 对每个资源有这明确的最优化缓存策略。
- 明确网站的最有的缓存级别。
- 最小化变动。

## 本地缓存

#### 内存缓存（Memory Cache）

内存中的缓存，主要包含的是当前页面中已经抓取到的资源，例如页面上已经下载的样式、脚本、图片等。读取内存中的数据很高效，但是缓存持续性很短，会随着进程的释放而释放。一旦我们关闭 Tab 页面，内存中的缓存也就被释放了。

#### 硬盘缓存（Disk Cache）

存储在硬盘中的缓存，读取速度慢点，但是什么都能存储到磁盘中，比之 Memory Cache 胜在容量和存储时效性上。\
在所有浏览器缓存中，Disk Cache 覆盖面基本上是最大的。它会根据 HTTP Header 中的字段判断哪些资源需要缓存，哪些资源可以不请求直接使用，哪些资源已经过期需要重新请求。并且即使在跨站点的情况下，相同地址的资源一旦被硬盘缓存下来，就不会再次去请求数据。绝大部分的缓存都来自 Disk Cache。

#### 推送缓存（Push Cache）

HTTP/2 中的内容，当以上三种缓存都没有命中时，它才会被时候用。它只在会话（Session）中存在，一旦会话结束就被释放，并且缓存时间也很短暂（大约5分钟）。

#### Cookie

服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。通常，它用于告知服务端两个请求是否来自同一浏览器，如保持用户的登录状态。Cookie 使基于无状态的HTTP协议记录稳定的状态信息成为了可能。

#### Storage

Web存储和cookie的存储形式很相似，都是使用键值对，但是Web存储的存储量更大，保存周期也不一样。Web存储分localStorage和sessionStorage两种，这两种只有在存储时间上有区别。localStorage相对来说是永久存储，sessionStorage的有效期只在会话期内。

这类存储一般存储比较大的业务数据，可以将一些更新频率低，更新信息不是很重要的数据存储，这样可以先加载本地数据，然后请求服务器，如果服务器数据发送变化再更新页面以及同步更新本地数据。这样在用户体验上比较不错，尤其对那些数据请求时间比较长的接口。还可以用来处理一些用户偏好类的信息。

```javascript
// Save data to the current local store
localStorage.setItem("username", "John");

// Access some stored data
alert( "username = " + localStorage.getItem("username"));
```

#### 离线缓存

通过在html元素的manifest属性来启用离线缓存，manifest的值是一个缓存清单列表，用来指定资源的缓存与备用。提供了navigator.onLine来判断是否离线，并通过ApplicationCache的对象来处理缓存。

对目前来说，离线缓存的意义并不大，无网络的情况下大部分网站都是没意义的，这里只是提及下有这种缓存方式。有一个非常的好场景使用了这种缓存方式，可以参考<http://devdocs.io>。

```html
<!DOCTYPE html>
<html manifest="/manifest.appcache">
...
</html>
```

manifest.appcache

```yaml
CACHE MANIFEST

example.html
banana100.png

FALLBACK:
* offline.html

NETWORK:
cherries100.png

CACHE:
apple100.png
```

#### Service Worker

一个还没定案的标准，大概意思就是在本地开启一个代理服务器，资源请求先经过本地服务器，本地服务器发现没有就去找代理服务，拿到资源后缓存在本地，下次请求的时候的就直接拿本地缓存。

```javascript
navigator.serviceWorker.register("/serviceworker.js").then(registration => {
  console.log("success!");
  if (registration.installing) {
    registration.installing.postMessage("Howdy from your installing page.");
  }
}, err => {
  console.error("Installing the worker failed!", err);
});
```

serviceworker.js

```javascript
self.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open('v1').then(function(cache) {
      return cache.addAll(['/index.html', '/style.css']);
    })
  );
});
self.addEventListener('fetch', function(event) {
  event.respondWith(// magic goes here);
});
```

### Indexed Database

这玩意儿目前还是草案，有部分浏览器实现了这个功能，但是都不是很规范。就是提供了一个本地数据库，应该是NoSQL类型的。