# 管道
使用管道转换字符串、货币金额、日期和其他数据以供显示。管道是在模板表达式中使用的简单函数，用于接受输入值并返回转换后的值。

### 内置管道
 - <DatePipe> 格式化时间
	- {{ value_expression | date [ :format [ : timezone [ : locale ] ] ] }}
	- ( format:string) 时间格式，默认值mediumDate
	- ( timezone:string ) 时区
	- ( local:string ) 本地化处理
- <UpperCasePipe> 文本转换大写
	- {{ value_expression | uppercase }}
- <LowerCasePipe> 文本转换成小写
	- {{ value_expression | lowercase }}
- <CurreryPipe> 格式化货币
	- {{ value_expression | currency [ : currencyCode [ : display [ : digitsInfo [ : locale ] ] ] ] }}
	- ( currencyCode:string ) 货币编码，默认值this._defaultCurrencyCode
	- ( display:string|boolean ) 是否展示code，如果传入字符串，code将变成自定义的字符串，如果false，则不展示code，默认符号
	- ( digitsInfo:string ) 数字信息格式化
	- ( local:string ) 本地化处理
- <DecimalPipe> 格式化数字
	- {{ value_expression | number [ : digitsInfo [ : locale ] ] }}
	- ( digitsInfo:string ) 数字信息格式化
	- ( local:string ) 本地化处理
- <PercentPipe> 格式化百分数
	- {{ value_expression | percent [ : digitsInfo [ : locale ] ] }}
	- ( digitsInfo:string ) 数字信息格式化
	- ( local:string ) 本地化处理
- <AsyncPipe> 从异步原语中解包一个值
	- {{ value_expression | async }}
- <I18nPluralPipe> 将值映射到根据语言环境规则将值复数的字符串
	- {{ value_expression | i18nPlural : pluralMap [ : locale ] }}
	- ( pluralMap:object ) 模仿ICU格式的对象
	- ( local:string ) 本地化处理
- <I18nSelectPipe> 显示与当前值匹配的字符串的通用选择器
	- {{ value_expression | i18nSelect : mapping }}
	- ( mapping:object ) 一个对象，指示应为所提供值的不同值显示的文本。
- <JsonPipe> 格式化成JSON字符串
	- {{ value_expression | json }}
- <KeyValuePipe> 将对象转换键值对的数组
	- {{ value_expression | keyvalue [ : compareFn ] }}
	- ( compareFn:(a:  [KeyValue]<K, V>, b:  [KeyValue]<K, V>) => number ) 对比函数，默认：defaultComparator
- <SlicePipe> 切割一个新数组
	- {{ value_expression | slice : start [ : end ] }}
	- ( start:number ) 开始位置
	- ( end:number ) 结束位置
- <TitleCasePipe> 将文本转换为标题大小写。将每个单词的第一个字母大写，并将单词的其余部分转换为小写
	- {{ value_expression | titlecase }}

**数字信息格式化**
- ( digitsInfo:string ) 展示格式为{minIntegerDigits}.{minFractionDigits}-{maxFractionDigits}，默认值1.2-2
	- minIntegerDigits 整数位最小位数
	- minFractionDigits 小数位最小位数
	- maxFractionDigits 小叔为最大位数

### 自定义管道
1）使用ng生成命令或者手动创建一个管道
```ts
@Pipe({name: 'pow'})
export class PowPipe implements PipeTransform {
	// 定义通道的参数
  transform(value: number, exponent = 1): number {
    return Math.pow(value, exponent);
  }
}
```

2)在moudule上注册管道
```ts
@NgModule({
    imports: [],
    declarations: [PowPipe],
    providers: [],
    bootstrap: [AppComponent],
})
export class AppModule {}
```

3）在模板上使用管道
```html
<p>{{ 2 | pow:2 }}</p>
```
