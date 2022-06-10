# React Apis

##### React.Component

React组件的基础类，通过继承该类实现一个组件。

```jsx
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```



##### React.PureComponent

功能类似React.Component，该类实现了默认的shouldComponentUpdate()生命周期函数。



##### React.Fragment

将多个元素组合在一起，且不需要创建额外的DOM元素。

```jsx
render() {
  return (
    <React.Fragment>
      Some text.
      <h2>A heading</h2>
    </React.Fragment>
  );
}
```



##### React.Suspense

指定加载指示器，以防它下面的树中的某些组件尚未准备好渲染。主要用于组件的动态加载，动态加载只能放在该组件下。

```jsx
const OtherComponent = React.lazy(() => import('./OtherComponent'));
function MyComponent() {
  return (
    <React.Suspense fallback={<Spinner />}>
      <div>
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}
```



##### React.memo()

提供一个HOC，只要用来封装函数组件，提供函数组件的性能优化，根据判断防止组件无意义的渲染。

```jsx
function MyComponent(props) {
  /* render using props */
}
function areEqual(prevProps, nextProps) {
  /*
  return true if passing nextProps to render would return
  the same result as passing prevProps to render,
  otherwise return false
  */
}
export default React.memo(MyComponent, areEqual);
```



##### React.createElement()

创建一个指定类型的元素。类型可以是DOM标签，也可以是组件。

```jsx
React.createElement(
  type,
  [props],
  [...children]
)
```



##### React.createFactory()

创建一个生成指定类型元素的工厂函数。一般用于不使用jsx的项目。

```jsx
React.createFactory(type)
```



##### React.cloneElement()

克隆一个指定的元素。

```jsx
React.cloneElement(
  element,
  [config],
  [...children]
)
// 类似于
<element.type {...element.props} {...props}>{children}</element.type>
```



##### React.isValidElement()

校验一个对象是不是合法元素。

```jsx
React.isValidElement(object)
```



##### React.createRef()

创建一个可以通过 ref 属性附加到元素的引用。

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.inputRef = React.createRef();
  }
  render() {
    return <input type="text" ref={this.inputRef} />;
  }
  componentDidMount() {
    this.inputRef.current.focus();
  }
}
```



##### React.forwardRef()

提供一个HOC，实现函数组件的引用透传。

```jsx
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```



##### React.createContext()

创建一个上下文对象。该对象包含Context.Provider和Conetxt.Consumer两个组件。

```jsx
const ThemeContext = React.createContext('white');
```



##### React.lazy()

提供一个HOC，配合import实现组件的动态加载。同时也提供了代码分割的功能。

```jsx
const SomeComponent = React.lazy(() => import('./SomeComponent'));
```



##### React.startTransition()

允许提供的回调函数中的变化标记为更新。

```jsx
React.startTransition(callback)
```



### React.Children

提供一系列功能去处理props.children的数据结构。

##### React.Children.map()

将此设置为 thisArg 的子项中包含的每个直接子项调用一个函数。并返回一个处理后的数组。

```jsx
React.Children.map(children, function[(thisArg)])
```



##### React.Children.forEach()

类似前面的map，但是不返回数组。

```jsx
React.Children.forEach(children, function[(thisArg)])
```



##### React.Children.count()

返回直接子元素的个数。

```jsx
React.Children.count(children)
```



##### React.Children.only()

验证只有一个元素并返回它。否则此方法会引发错误。

```jsx
React.Children.only(children)
```



##### React.Children.toArray()

以数组的形式返回子元素不透明数据结构，并为每个子元素分配key。

```jsx
React.Children.toArray(children)
```



### React 实例

##### setState()

将组件状态的更改排入队列并告诉 React 该组件及其子组件需要使用更新后的状态重新渲染。参数updater可以是下一个状态，也可以是下一个状态的生成函数。callback是一个回调函数，可选，但组件完成更新后，会回调该函数。

```jsx
setState(updater[, callback])

updater = (state, props) => stateChange
```



##### forceUpdate()

强制更新组件，无视shouldComponenetUpdate()的优化，一般避免调用该方法。

```
forceUpdate(callback)
```



### Hooks

##### useState()

返回一个有状态的值和一个更新状态的函数，调用该函数可以触发组件更新。可以传入一个初始值。

```JSX
const [state, setState] = useState(initialState);
```

初始值可以是一个直接量，也可以是值生成函数。

```jsx
useState(0);
// or
useState(() => {
  return 1 + 1;
});
```

更新状态函数也可以传入一个直接量，或者状态生成函数。

```jsx
setState(1);
// or
setState(prevState => {
  return nextState;
})
```



##### useEffect()

该hook在函数组件中处理副作用，例如数据抓取、事件绑定、订阅等。如果没有传入dependencies，则默认每次render之后调用didUpdate，传入deps之后，根据deps发生变化后调用didUpdate。如果传入的是个空数组，则只会调用一次。didUpdate函数可以返回一个cleanup函数，cleanup函数会在组件移除前调用。如果一个组件多次渲染（通常是这样），则在执行下一个didUpdate之前会调用cleanup函数。

```jsx
useEffect(didUpdate[, deps]);
```



##### useContext()

接收一个React.Context，并返回该context的值。

```jsx
const value = useContext(MyContext);
```



##### useReducer()

可以替代useState的状态管理hook。reducer是一个状态工厂函数，会接收上一个状态和动作，并返回下一个状态。initialArg可以提供一个初始参数给init函数。init函数拿到initialArg后生成一个初始状态。dispatch派发一个动作给reducer函数。

```jsx
const [state, dispatch] = useReducer(reducer, initialArg, init);
reducer = (prevState, action) => newState
init = (initialArg) => initialState
```



##### useCallback()

返回一个缓存函数。

```jsx
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```



##### useMemo()

返回一个缓存值。

```jsx
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```



##### useRef()

useRef 返回一个可变 ref 对象，其 .current 属性初始化为传递的参数 (initialValue)。返回的对象将在组件的整个生命周期内持续存在。效果等同于React.createRef()。

```jsx
const refContainer = useRef(initialValue);
```



##### useImperativeHandle()

useImperativeHandle 自定义使用 ref 时暴露给父组件的实例值。与往常一样，在大多数情况下应避免使用 refs 的命令式代码。 useImperativeHandle 应该与 forwardRef 一起使用。

```jsx
useImperativeHandle(ref, createHandle[, deps])

function FancyInput(props, ref) {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));
  return <input ref={inputRef} ... />;
}
FancyInput = forwardRef(FancyInput);
```



##### useLayoutEffect()

与 useEffect 相同，但它在所有 DOM 变化后同步触发。使用它从 DOM 中读取布局并同步重新渲染。在 useLayoutEffect 中安排的更新将在浏览器有机会绘制之前同步刷新。尽可能首选标准 useEffect 以避免阻塞视觉更新。

```jsx
useLayoutEffect(didUpdate[, deps]);
```



##### useDebugValue()

用于在 React DevTools 中显示自定义hook的标签。format是数据格式化函数，可选。

```jsx
useDebugValue(value[, format]);
format = value => newValue;
```



##### useDeferredValue()

接受一个值并返回该值的新副本，该副本将推迟到更紧急的更新。如果当前渲染是紧急更新的结果，比如用户输入，React 将返回之前的值，然后在紧急渲染完成后渲染新的值。使用它可以去抖动或节流来延迟更新，使用该hook的好处是 React 将在其他工作完成后立即进行更新（而不是等待任意时间），并且像 startTransition 一样，延迟值可以暂停而不会触发现有内容的意外回退。

```jsx
const deferredValue = useDeferredValue(value);
```



##### useTransition()

返回转换的挂起状态的有状态值，以及启动它的函数。

```jsx
const [isPending, startTransition] = useTransition();
startTransition(() => {
  setState(newState);
})

```



##### useId()

用于生成在服务器和客户端之间稳定的唯一 ID 的 hook，同时避免重复。

```jsx
const id = useId();
```



##### useSyncExternalStore()

推荐用于以与 selective hydration 和时间切片等并发渲染功能兼容的方式从外部数据源读取和订阅的hook。

subscribe：函数注册一个回调，每当商店更改时调用该回调。

getSnapshot：返回存储的当前值的函数。

getServerSnapshot：返回服务器渲染期间使用的快照的函数。

```jsx
const state = useSyncExternalStore(subscribe, getSnapshot[, getServerSnapshot]);
const state = useSyncExternalStore(store.subscribe, store.getSnapshot);
const selectedField = useSyncExternalStore(
  store.subscribe,
  () => store.getSnapshot().selectedField,
  () => INITIAL_SERVER_SNAPSHOT.selectedField,
);
```



##### userInsertionEffect()

与 useEffect 相同，但它在所有 DOM 变化之前同步触发。在 useLayoutEffect 中读取布局之前，使用它将样式注入 DOM。由于此挂钩的范围有限，因此此挂钩无法访问 refs 并且无法安排更新。

```jsx
useInsertionEffect(didUpdate);
```



## ReactDOM

##### createPortal()

创建一个门户。门户提供了一种将子节点渲染到 DOM 节点中的方法，该 DOM 节点存在于 DOM 组件的层次结构之外。

```jsx
createPortal(child, container)
```



##### flushSync()

强制 React 同步刷新提供的回调中的任何更新。这确保了 DOM 立即更新

```jsx
flushSync(callback)
```



##### render()

将 React 元素渲染到所提供容器中的 DOM 中，并返回对组件的引用（或为无状态组件返回 null）。

```
render(element, container[, callback])
```



##### hydrate()

与 render() 相同，但用于合成 HTML 内容由 ReactDOMServer 渲染的容器。 React 将尝试将事件侦听器附加到现有标记。

```jsx
hydrate(element, container[, callback])
```



##### unmountComponentAtNode()

从 DOM 中移除已安装的 React 组件并清理其事件处理程序和状态。在v18后被root.unmout()替代。

```jsx
unmountComponentAtNode(container)
```



##### findDOMNode()

如果此组件已安装到 DOM 中，则返回相应的实际 DOM 元素。

```jsx
findDOMNode(component)
```



##### createRoot()

为提供的容器创建一个 React 根并返回根。 root 可用于通过 render 将 React 元素渲染到 DOM 中。

```jsx
createRoot(container[, options]);
```



##### root.render()

功能与ReactDOM.render()一致。

```jsx
const root = createRoot(container);
root.render(element);
```



##### root.unmount()

功能与ReactDOM.unmountComponentAtNode()一致。

```jsx
const root = createRoot(container);
root.unmount();
```



##### hydrateRoot()

与 createRoot() 相同，但用于合成 HTML 内容由 ReactDOMServer 呈现的容器。 React 将尝试将事件侦听器附加到现有标记。

```jsx
hydrateRoot(container, element[, options])
```