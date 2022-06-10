# React生命周期

![image-20220609145324815](/Users/bytedance/Library/Application Support/typora-user-images/image-20220609145324815.png)

明确的生命周期函数只有类组件才有，函数组件只能通过HOC或者Hook来模拟。

- Mounting

    - construtor()	
    - static getDerivedStateFormProps()
    - render()
    - componentDidMount()

- Updating

    - static getDerivedStateFormProps()

    - shouldComponentUpdate()

    - render()

    - getSnapshotBeforeUpdate()

    - componentDidMount()

- Umounting
    - componentWillUnmount()

- Error Handling

    - static getDerivedStateFromError()

    - componentDidCatch()

### 常用函数

##### render()

渲染函数，是类组件必须要实现的函数。当state或props发生变化时，会调用该函数，当调用时会返回以下值：

- 元素
- 元素数组或元素片段
- 入口
- 字符串或数字
- true、false、null

其他值都是非法的。如果shouldComponentUpdate()返回false，则不会调用render()

```jsx
render()
```



##### contructor()

类组件的实例函数，如果不初始化一些数据，一般不用实现该函数。如果子类组件继承了父类组件，在该函数内需要强制调用super函数。

```jsx
constructor(props) {
  super(props);
  // Don't call this.setState() here!
  this.state = { counter: 0 };
  this.handleClick = this.handleClick.bind(this);
}
```



##### componentDidMount()

当组件被挂载DOM树上的时候触发，且只会触发一次。在该函数可以读取实际的DOM节点，然后进行事件绑定或者订阅。可以直接调用setState()。

```jsx
componentDidMount()
```



##### componentDidUpdate()

当组件挂载的实际DOM节点被更新的时候触发。在该函数内调用setState()需要添加条件限制，不然会出现死循环。

```jsx
componentDidUpdate(prevProps, prevState, snapshot)
```



##### componentWillUnmount()

当组件的挂载的实际DOM节点被移除的时候触发。在该函数内不可调用setState()，可以进行解绑事件或者退订等。

```jsx
componentWillUnmount()
```



##### shouldComponentUpdate()

该函数在更新阶段，render()之前调用，如果该函数返回false，则中断更新流程，不调用render()。但是调用forceUpdate()会跳过该函数。该函数可以用来做性能优化使用。

```jsx
shouldComponentUpdate(nextProps, nextState)
```



##### static getDerivedStateFromProps()

该函数在render()之前调用，不管是挂载阶段还是更新阶段，在更新阶段该函数调用之后调用shouldComponentUpdate()。该函数可以获得当前的props和上一次的state，需要返回null或新的state。如果返回null，表示不更新任何东西。

```jsx
static getDerivedStateFromProps(props, state)
```



##### getSnapshotBeforeUpdate()

该函数在componentDidUpdate()之前，在render()之后。该函数返回的值会传递给componentDidUpdate()的第三个参数。

```jsx
getSnapshotBeforeUpdate(prevProps, prevState)
```



##### static getDerivedStateFromError()

在子组件抛出错误后调用该函数，接受作为参数抛出的错误，并应返回一个state，并更新组件。

```jsx
static getDerivedStateFromError(error)
```



##### componentDidCatch()

在子组件抛出错误后调用该函数。

```jsx
componentDidCatch(error, info)
```