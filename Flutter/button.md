# 按钮组件

主要有悬浮按钮 `ElevatedButton()`、文本按钮 `TextButton()`、线性按钮 `OutlinedButton()`、图标按钮 `IconButton()` 以及面性按钮 `FilledButton()`。除了图标按钮外，其他按钮都可以用 `.icon()` 实现带图标的按钮。 

### 使用

```dart
const ElevatedButton(onPressed: () {}, child: const Text('normal'));
const TextButton(onPressed: () {}, child: const Text('Submit'));
const OutlinedButton( onPressed: () {}, child: const Text('Outlined Button'));
const IconButton( onPressed: () {}, icon: Icon(Icons.thumb_up));
const ElevatedButton.icon( onPressed: () {}, icon: Icon(Icons.send), label: const Text('发送'));
const OutlinedButton.icon( onPressed: () {}, icon: Icon(Icons.add), label: const Text('添加'));
const TextButton.icon( onPressed: () {}, icon: Icon(Icons.info), label: const Text('详情'));
const FilledButton(onPressed: () {}, child: const Text('Fill Button'));
```

### 属性

-   autofocus: 自动聚焦
-   child: 按钮文本
-   onFocusChange: 焦点变化事件
-   onHover: 悬停事件
-   onLongPress: 长按事件
-   onPressed: 点按事件
-   style: 按钮样式

### ButtonStyle

如果属性是 `MaterialStateProperty<T?>?` 则需要使用 `MaterialStateProperty.resolveWith()` 来处理。

-   alignment: 按钮内容对齐方式
-   animationDuration: 长按动画时长
-   backgroundColor: 设置背景色
-   elevation: 设置阴影大小
-   fixedSize: 设置固定大小
-   foregroundColor: 状态设置前景色
-   iconColor: 图标颜色
-   iconSize: 图标大小
-   maximumSize: 最大尺寸
-   minmumSize: 最小尺寸
-   mouseCursor: 鼠标光标
-   overlayColor: 当按钮被聚焦、悬停或者按压时，按钮的背景色
-   padding: 填充尺寸
-   shadowColor: 阴影颜色
-   shape: 按钮形状
-   side: 按钮边框设置
-   tapTargetSize: 设置可按压区域
-   textStyle: 文本样式

### ButtonShape

-   BeveledRectangleBorder
-   CircleBorder
-   ContinuousRectangleBorder
-   LinearBorder
-   MaterialStateOutlinedBorder
-   RoundedRectangleBorder
-   StadiumBorder
-   StartBoder
