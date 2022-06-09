# React生命周期

![image-20220609145324815](/Users/bytedance/Library/Application Support/typora-user-images/image-20220609145324815.png)

明确的生命周期函数只有类组件才有，函数组件只能通过HOC或者Hook来模拟。



### Mounting

##### construtor()

##### static getDerivedStateFormProps()

##### render()

##### componentDidMount()



### Updating

##### static getDerivedStateFormProps()

##### shouldComponentUpdate()

##### render()

##### getSnapshotBeforeUpdate()

##### componentDidMount()



### Umounting

##### componentWillUnmount()



### Error Handling

##### static getDerivedStateFromError()

##### componentDidCatch()