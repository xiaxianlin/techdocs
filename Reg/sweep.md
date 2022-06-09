# 环视
环视结构不匹配任何字符，只匹配文本中的**特定位置**。

### 分类

**顺序环视**：顺序（从左到右）查看文本
**逆序环视**：逆序（从右到左）查看文本
**肯定环视**：匹配成功则表示命中
**否定环视**：匹配失败则表示命中

- **(?=)**肯定顺序环视：子表达式能够匹配右侧文本
- **(?!)**否定顺序环视：子表达式不能匹配右侧文本
- **(?<=)**肯定逆序环视：子表达式能够匹配左侧文本
- **(?<!)**否定逆序环视：子表达式不能匹配左侧文本

### 示例
1）肯定顺序环视
```js
'Total dollors: 123'.replace(/(?=123)/g, "$")
// 'Total dollors: $123'
```

2)肯定逆序环视
```js
'Total dollors: 123'.replace(/(?<=123)/g, "$")
'Total dollors: 123$'
```

3)否定顺序环视
```js
'Total dollors: 123'.replace(/(?!123)/g, "$")
// '$T$o$t$a$l$ $d$o$l$l$o$r$s$:$ 1$2$3$''
```

4)否定逆序环视
```js
'Total dollors: 123'.replace(/(?<!123)/g, "$")
// '$T$o$t$a$l$ $d$o$l$l$o$r$s$:$ $1$2$3'
```

5) 数字格式化’,’表达
```js
let num = "12345678"

num.replace(/(?=(\d\d\d))/g, ',')
// ',1,2,3,4,5,678'

num.replace(/(?=(\d\d\d)(?!\d))/g, ',')
// '12345,678'

num.replace(/(?=(\d\d\d)+$)/g, ',')
// '12,345,678'

num = "The population of 12345678 is growing"

num.replace(/(?=(\d\d\d)+$)/g, ',')
// 'The population of 12345678 is growing'

num.replace(/(?=(\d\d\d)+(?!\d))/g, ',')
// 'The population of 12,345,678 is growing'

num = "This is 2020s"
num.replace(/(?=(\d\d\d)+(?!\d))/g, ',')
// 'This is 2,020s'
```
