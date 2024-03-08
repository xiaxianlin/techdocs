# **文本组件**

文本组件负责展示单个样式的字符串。主要有 3 个，分别是 `Text()` 、`Text.rich()` 以及 `TextSpan()`。使用 `TextStyle()` 对文本进行样式设置。

### 使用

```dart
const Text("Hello world.");
```

Text.rich() 和 TextSpan() 可以构成复杂样式的文本展示。

```dart
const Text.rich(
    TextSpan(
        text: 'Hello',
        children: <TextSpan>[
            TextSpan(text: ' beautiful ', style: TextStyle(fontStyle: FontStyle.italic)),
            TextSpan(text: 'world', style: TextStyle(fontWeight: FontWeight.bold)),
        ]
    );
);
```

使用 `DefaultTextStyle()` 提供统一的文本样式，搭配 TextStyle.inherit 属性定制独立样式。

```dart
const DefaultTextStyle(
	style: TextStyle(color: Colors.red, fontSize: 20.0),
    textAlign: TextAlign.left,
    child: Column(
    	children:<Widget>[
      		Text("Hello"),
            Text(" world.", style:TextStyle(inherit: false, color: Colors.green)),
        ],
    ),
);
```

使用 `SelectableArea()` 和 `SelectionContainer` 设置文本的可选和禁选。

```dart
const SelectionArea(
    child: Column(
        children: <Widget>[
            Text('Selectable text'),
            SelectionContainer.disabled(child: Text('Non-selectable text')),
            Text('Selectable text'),
        ],
    ),
)
```

### 参数

-   maxLines: 最大行数
-   overflow: 溢出处理
-   selectionColor: 被选中文本的颜色
-   softWrap: 文本是否应在软换行符处换行
-   strutStyle: 使用的支柱样式。支柱样式定义了支柱，该支柱设置了最小的垂直布局度量。
-   style: 样式
-   textAlign: 文本对齐方式
-   textDirection: 文本方向
-   textScaleFactor: 缩放因子
-   textSpan: 在 TextSpan 里面行内展示
-   textWidthBasis: 渲染文本宽度的计算方式

### TextStyle

-   background: 背景
-   backgroundColor: 背景色
-   color: 颜色
-   decoration: 装饰线
-   decorationColor: 装饰线颜色
-   decorationStyle: 装饰线样式
-   decorationThickness: 装饰描边的厚度，是字体定义的厚度的倍数。
-   fontFamily: 字体
-   fontFeatures: 影响字体选择字形方式的字体特性列表
-   fontSize: 字体大小
-   fontStyle: 字体样式
-   fontWeight: 字体粗细
-   forground: 前景
-   height: 行高
-   inherit: 是否继承默认样式
-   letterSpacing: 字间距
-   overflow: 文本溢出处理
-   shadows: 文本阴影
-   textBaseline: 文本基线
-   wordSpacing: 在每个空白序列（即每个单词之间）添加的空间大小（以逻辑像素为单位）。负值可用于拉近字与字之间的距离。

