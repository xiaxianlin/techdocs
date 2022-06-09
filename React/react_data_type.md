# React的数据类型
在了解具体实现，首先要了解基本常量，尤其是类型常量，知道大概有哪些类型，简单的了解是啥，对整个源码阅读会更顺畅一些。

## 核心数据结构

```javascript
{
    @@typeof: REACT_ELEMENT_TYPE,
    ...
}
```

@@typeof主要取值ReactSymbols，标识该数据对象的类型，这些标识数据对象主要用来描述ReactTypes。除了这个字段外，还有ReactType的其他信息字段。

## ReactTypes
ReactTypes提供了React主要的数据类型描述。

- ReactText：文本，（string | number）
- ReactPortal：片段
- ReactFragment：片段，处理没有父类包裹的节点
	- ReactEmpty
	- Iterable<React$Node>：ReactNode集合
- ReactProvider：供应商
- ReactConsumer：消费者
- ReactNode：节点，抽象类型，以下六类的统称
	- React$Element：元素，主要是Component，也包括下面五类
	- ReactPortal
	- ReactText
	- ReactFragment
	- ReactProvider
	- ReactConsumer
- ReactEmpty：空（null | void | boolean）
- ReactNodeList：这玩意儿不懂是干啥的
	- ReactEmpty
	- React$Node	
- ReactProviderType：供应商类别
- ReactContext：上下文
- RefObject：引用对象

## ReactSymbols
就是一些类型集合，包括元素类型和模式类型等。

- REACT_ELEMENT_TYPE：元素类型
- REACT_PORTAL_TYPE：入口类型
- REACT_FRAGMENT_TYPE：片段类型
- REACT_STRICT_MODE_TYPE：严格模式类型，在开发环境使用
- REACT_PROFILER_TYPE：分析器类型
- REACT_PROVIDER_TYPE：上下文供应商类型
- REACT_CONTEXT_TYPE：上下文类型，消费者也使用该类型
- REACT_ASYNC_MODE_TYPE：好像被REACT_CONCURRENT_MODE_TYPE取代了
- REACT_CONCURRENT_MODE_TYPE：异步渲染模式类型
- REACT_FORWARD_REF_TYPE：引用类型
- REACT_SUSPENSE_TYPE：延迟渲染类型，需要搭配REACT_LAZY_TYPE一起使用
- REACT_MEMO_TYPE：纯函数组件类型，类似PureComponent
- REACT_LAZY_TYPE：懒加载类型

## ReactWorkTags
根据不同的工作标签，Fiber去寻找不同的处理方式。

- FunctionComponent： 函数组件
- ClassComponent：类组件
- IndeterminateComponent：不明确组件，还没有区分是类组件还是函数组件之前
- HostRoot：宿主树的根节点，可以嵌入到其他节点
- HostPortal：宿主子树，可以在不同的入口渲染
- HostComponent：宿主组件
- HostText：宿主文本
- Fragment：片段
- Mode：模式组件，主要是异步模式和严格模式
- ContextConsumer：上下文消费者
- ContextProvider：上下文供应商
- ForwardRef：穿越引用
- Profiler：分析器
- SuspenseComponent：阻塞组件
- MemoComponent：纯函数组件，效果类似PuerComponent
- SimpleMemoComponent：MemoComponent的简单版，不需要传入比较函数
- LazyComponent：懒加载组件
- IncompleteClassComponent：不完整的类组件，异常是出现

## ReactSideEffectTags

- NoEffect
- PerformedWork
- LifecycleEffectMask
	- Update
	- Passive
	- Callback
	- Snapshot
- Placement
- PlacementAndUpdate
- Deletion
- ContentReset
- DidCapture
- Ref
- HostEffectMask
- Incomplete
- ShouldCapture

## ReactType0fMode

- NoContext = 0b000 
- ConcurrentMode = 0b001 : 同步模式，值为1
- StrictMode = 0b010 ： 严格模式，值为2
- ProfileMode = 0b100 ： 分析器模式，值为4
