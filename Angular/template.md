# 模板
模板是一种HTML片段，使用NG带有的特殊语法。

### 文本插入
使用**{{}}**符号插入文本，**{{}}**里面可以是字面量，也可以是变量或方法。该语法可以运行部分表达式。在访问变量名的时候也有作用域限制。
```html
<!-- 支持字面量 -->
<p>{{1}}</p>
<!-- 支持访问属性 -->
<p>{{name}}</p>
<!-- 支持调用方法 -->
<p>{{getName()}}</p>
<!-- 支持加法运算 -->
<p>{{ 1 + 1 + getValue() }}</p>
<!-- 支持访问遍历变量 -->
<div *ngFor="let customer of customers">{{customer.name}}</div>
<!-- 支持访问本地变量 -->
<div><input #customerInput>{{customerInput.value}}</div>
```

### 模板语句
模板语句是可以在HTML中用于响应用户事件的方法或属性。使用模板语句，应用程序可以通过显示动态内容或提交表单等操作来吸引用户。
```html
<!-- 元素事件回调 -->
<button (click)="onSave($event)">Save</button>
<!-- 自定义指令 -->
<button *ngFor="let hero of heroes" (click)="deleteHero(hero)">{{hero.name}}</button>
<!-- 自定义事件 -->
<form #heroForm (ngSubmit)="onSubmit(heroForm)"> ... </form>
```

### 管道
使用管道转换字符串、货币金额、日期和其他数据以供显示。管道是在模板表达式中使用的简单函数，用于接受输入值并返回转换后的值。

```html
<!-- 日期转换 -->
<p>The hero's birthday is {{ birthday | date }}</p>
<!-- 带参数的数字转换 -->
<p>Decimal: {{8799.12345 | number:'5.1-3'}}</p>  <!-- 08799.123
<!-- 多通道转换，先转成日期，在全部大小写 -->
<p>{{birthday | date | upper}}</p>
```

### 数据绑定
对元素使用**[]**来修饰，表示该属性绑定了一个数据引用。如果不加[]表示该属性接受一个静态值。

对元素的DOM属性进行绑定，属性名称是元素对应的DOM对象的属性。
```html
<!-- imageSrc就是组件的一个属性 -->
<img [src]="imageSrc"/>
<!-- 这里的colSpan不能写成colspan，因为colSpan才是TD对应的DOM对象的属性 -->
<td [colSpan]="colspan"></td>
```

对自定义的组件的输入属性进行数据绑定。
```html
<app-item-detail [childItem]="parentItem"></app-item-detail>
```

对元素的标签属性进行绑定，使用[attr.<name>]形式，如果该引用的值为null和undefined就移除该属性。
```html
<!-- 绑定id -->
<button [attr.id]="id"></button> 
<!-- 绑定colspan -->
<td [attr.colspan]="colspan"></td>
```

还可以针对样式属性进行绑定。
```html
<!-- 效果：class="active selected" -->
<p [class.active]="active" [class.selected]="selected"></p>
<!-- 效果：style="color: orange; margin-top: 30px;" -->
<p [style.color]="color" [style.margin-top]="margin"></p>
```

### 事件绑定
使用**()**对事件进行绑定。
```html
<button (click)="onSave()">Save</button>
```

### 双向绑定
使用**[()]**对数据进行双向绑定。
```html
<input [(ngModel)]="currentItem.name" id="example-ngModel">
```

### 模板变量
使用**#**进行模板变量的声明。
```html
<input #phone placeholder="phone number" />
```
