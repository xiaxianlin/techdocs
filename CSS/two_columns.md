# 两栏布局
> 要求左栏定宽，右栏自适应。

**DOM 结构**

```html
<div class="container">
    <div class="item left"></div>
    <div class="item right"></div>
</div>
```

**基本样式**

```css
.container {
    width: 100%;
    height: 450px;
    border: 1px solid red;
}
.item {
    height: 450px;
}
.left {
    width: 100px;
    background: grey;
}
.right {
    background: orange;
}
```

### float 布局

**1）使用 BFC 特性**

> 父级使用 overflow:hidden 后会清除 float 的效果，从而把右边挤开。

```css
.container {
    overflow: hidden;
}
.left {
    float: left;
}
```

**2）传统模式**

> 使用 margin 把 float 覆盖的区域避开，修改定宽时需要同时修改两个属性。

```css
.left {
    float: left;
}
.right {
    margin-left: 100px;
}
```

### position 布局

> 和 float 类似，将左栏绝对定位，右栏避开覆盖区域；可以通过父级的 padding 和右栏的 margin 来避开。

```css
.container {
    position: relative;
    // padding-left: 100px;
}
.left {
    position: absolute;
    top: 0;
    left: 0;
}
.right {
    margin-left: 100px;
}
```

### flex 布局

> 根据 flow-grow 的来进行布局，左栏不设置 flex-grow，右栏随意设置 flex-grow。

```css
.container {
    display: flex;
}
.right {
    flex-grow: 1;
}
```

### table 布局

> 使用 table 特性，可以直接使用 table 标签，或者是 css 的 table 属性，不推荐使用 table 布局。

```css
.container {
    display: table;
}
.item {
    display: table-cell;
}
```

### grid 布局

> 使用最新的网格布局模式，不过支持率不高。修改 grid-template-columns 的

```css
.container {
    display: grid;
    grid-template-columns: 100px 1fr;
}
.left {
    width: auto;
    grid-column: 1;
}
```
