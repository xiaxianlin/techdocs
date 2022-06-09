# 指令
指令是向NG应用程序中的元素添加其他行为的类。使用NG的内置指令来管理表单、列表、样式和用户看到的内容。

### 分类
- 组件：带有模板的指令
- 属性指令：更改元素、组件或其他指令的外观或行为的指令
- 结构指令：通过添加和删除DOM元素来更改DOM布局的指令

### 内置指令
- NgClass：添加和删除一组CSS样式名
- NgStyle：添加和删除一组HTML样式
- NgModel：给HTML表单元素添加双向数据绑定
- *NgIf：有条件地从模板创建或释放子视图
- *NgFor：对列表中的每个项目重复一个节点
- *NgSwitch：在备用视图之间切换的一组指令
	- NgSwitchCase
	- NgSwitchDefault

### 结构指令的语法
```ts
*:prefix="( :let | :expression ) (';' | ',')? ( :let | :as | :keyExp )*"
```

- prefix
- key
- local
- export 
- expression
