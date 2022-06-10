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



#### React.Children

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
setState(updater, [callback])

updater = (state, props) => stateChange
```



##### forceUpdate()

强制更新组件，无视shouldComponenetUpdate()的优化，一般避免调用该方法。

```
forceUpdate(callback)
```



#### Hooks

##### useState()

##### useEffect()

##### useContext()

##### useReducer()

##### useCallback()

##### useMemo()

##### useRef()

##### useImperativeHandle()

##### useLayoutEffect()

##### useDebugValue()

##### useDeferredValue()

##### useTransition()

##### useId()

##### useSyncExternalStore()

##### userInsertionEffect()



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