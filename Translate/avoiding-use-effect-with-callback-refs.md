# 避免 useEffect 和 CallbackRef 结合使用

> 原文地址：[Avoiding useEffect with callback refs](https://tkdodo.eu/blog/avoiding-use-effect-with-callback-refs)

尽管 refs 是可变容器，理论上我们可以在其中存储任意值，但它们最常用于访问 DOM 节点：

```jsx
const ref = React.useRef(null)

return <input ref={ref} defaultValue="Hello world" />
```

`ref`是内置原语的保留属性，React 将在渲染后存储 DOM 节点。卸载组件时，它将被设置回*null 。*

## 使用 refs 进行交互

对于大多数交互，不需要访问底层 DOM 节点，因为 React 会自动为我们处理更新。可能需要参考的一个很好的例子是焦点管理。

[Devon Govett](https://twitter.com/devongovett) 有一个很好的 RFC [建议](https://github.com/devongovett/rfcs-1/blob/patch-1/text/2019-focus-management.md)将 FocusManagement 添加到 react-dom，但是现在，React 中没有任何东西可以帮助我们。

### 使用 useEffect 进行聚焦

那么，现在您将如何在渲染后聚焦输入元素呢？（我知道存在[自动对焦](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/autofocus)，这是一个示例。如果这让您感到困扰，想象一下您想要为节点设置动画。）

好吧，我见过的大多数代码都试图这样做：

```jsx
const ref = React.useRef(null)

React.useEffect(() => {
  ref.current?.focus()
}, [])

7return <input ref={ref} defaultValue="Hello world" />
```

这大部分都很好，并且不违反任何规则。空的依赖数组是可以的，因为里面唯一使用的是 ref，它是稳定的。linter 不会抱怨将其添加到依赖数组中，并且在渲染期间也不会读取 ref（这对于并发 React 功能可能很麻烦）。

Effect 将在 “**onMount**” 时运行一次（[在严格模式下运行两次](https://reactjs.org/docs/strict-mode.html#ensuring-reusable-state)）。至此，React 已经将 DOM 节点对象写 ref 中去了，所以可以对节点进行聚焦操作。

然而，这不是最好的方法，并且在一些更高级的情况下确实有一些警告。

具体来说，它假定在 effice 运行时 ref 已 “**filled**”。如果它不可用，例如因为您将 ref 传递给自定义组件，该组件将延迟渲染或仅在其他用户交互后显示输入，则在效果运行时 ref 的内容仍将为 **null** 并且不会聚焦：

```jsx
function App() {
  const ref = React.useRef(null)

  React.useEffect(() => {
    // 🚨 ref.current is always null when this runs
    ref.current?.focus()
  }, [])

  return <Form ref={ref} />
}

const Form = React.forwardRef((props, ref) => {
  const [show, setShow] = React.useState(false)

  return (
    <form>
      <button type="button" onClick={() => setShow(true)}>
        show
      </button>
      // 🧐 ref is attached to the input, but it's conditionally rendered
      // so it won't be filled when the above effect runs
      {show && <input ref={ref} />}
    </form>
  )
})
```

这里代码执行情况：

-   渲染 Form 组件
-   input 组件未渲染，ref 依然为空
-   执行 effect 函数，但是什么都没做
-   渲染 input 组件，*ref* 将被填充，但不会被聚焦，因为 effect 函数不会再次运行

问题是 effect 函数 和 Form 组件的 render 函数是 “bound” 状态，而我们实际上想表达的是：“在 input 组件渲染时聚焦 input 输入框”，而不是“当 Form 组件挂载时”。

## Callback Refs

这就是 callback ref 发挥作用的地方。如果你看过 [refs 的类型声明](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/fc9b16957473f81a1d708e6948b8d61e292aeb58/types/react/v17/index.d.ts#L85)，我们可以看到我们不仅可以将 ref 对象传递给它，还可以传递一个函数：

```ts
type Ref<T> = RefCallback<T> | RefObject<T> | null
```

从概念上讲，我喜欢将 React 元素上的 refs 视为在组件渲染后调用的函数。此函数获取作为参数传递的渲染 DOM 节点。如果 React 元素卸载，它将再次使用*null*调用。

*因此，将 useRef* （一个 RefObject）中的 ref 传递给 React 元素只是语法糖：

```jsx
<input
  ref={(node) => {
    ref.current = node;
  }}
  defaultValue="Hello world"
/>
```

<center style="font-weight: bolder;font-size: 16px;">再次强调这一点：所有 ref prop 都只是函数！</center>

这些函数是在渲染后运行的，在函数内部执行其他操作完全没问题。如果 ref 只是被称为 *onAfterRender* 或其他东西，也许会更好。

有了这些知识，是什么阻止我们将输入集中在回调 ref 中，我们可以直接访问节点？

```jsx
<input
  ref={(node) => {
    node?.focus()
  }}
  defaultValue="Hello world"
/>
```

嗯，有一个小细节：React 在 ***每次*** 渲染这个组件后都会运行这个函数。因此，除非我们可以经常 focus 组件（我们可能不会），否则我们必须告诉 React 只在我们想要的时候运行它。

### 使用 useCallback 来挽救

幸运的是，React 使用引用稳定性来检查回调 ref 是否应该运行。这意味着如果我们将相同的 ref（ref，双关语）传递给它，将跳过执行。

这就是 *useCallback* 的用武之地，因为这是我们确保不会不必要地创建函数的方式。也许这就是为什么它们被称为 **callback-refs** ，因为需要一直使用 *useCallback* 去封装它。😂

这是最终的解决方案：

```jsx
const ref = React.useCallback((node) => {
  node?.focus()
}, [])

return <input ref={ref} defaultValue="Hello world" />
```

与初始版本相比，它的代码更少，并且只使用一个钩子而不是两个。此外，它适用于所有情况，因为 callback ref 绑定到 DOM 节点的生命周期，而不是挂载它的组件的生命周期。此外，它不会在严格模式下执行两次（在开发环境中运行时），这对许多人来说似乎很重要。

正如[（旧）React 文档中这个隐藏的宝石](https://reactjs.org/docs/hooks-faq.html#how-can-i-measure-a-dom-node)所示，可以使用它来运行任何类型的操作，例如在其中调用 *setState* 。我将在此处留下示例，因为它实际上非常好：

```jsx
function MeasureExample() {
  const [height, setHeight] = React.useState(0)

  const measuredRef = React.useCallback(node => {
    if (node !== null) {
      setHeight(node.getBoundingClientRect().height)
    }
  }, [])

  return (
    <>
      <h1 ref={measuredRef}>Hello, world</h1>
      <h2>The above header is {Math.round(height)}px tall</h2>
    </>
  )
}
```

所以，如果你需要在渲染后直接与 DOM 节点交互，尽量不要直接跳转到 *useRef* + *useEffect*，而是考虑使用* refs*。