# Widget
Widget是构建Flutter视图的最基本元素，类似于React中万物皆可为组件的思想。从状态管理的角度，分为有状态和无状态，分别继承自**StatelessWidget**和**StatefulWidet**。从功能来看，主要分为容器组件、展示组件、布局组件和表单组件等。

### 无状态组件

### 有状态组件

### 展示组件
- <Text> 在应用内创建带样式的文本
- <RichText> 富文本组件，配合TextSpan使用
- <Image> 展示图片
	- NetworkImage(String url)赋值给image参数
	- Image.network(String src)
	- Image.file(File file)
	- Image.asset(String name)
	- Image.memory(Uint8List bytes)


### 容器组件
- <Container> 一个可见的矩形元素
- <Center> 组件居中展示
- <Align>


### 布局组件
- <Row> 水平方向的弹性布局
- <Column> 垂直方向的弹性布局
- <Stack> 相对定位的浮动布局
