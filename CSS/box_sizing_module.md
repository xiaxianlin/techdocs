# Box Sizing Module
## 术语
- size： 一个一维或二维的测量，主要是width/height
- inner size： 内尺寸，box里面content-box的尺寸
- outer size： 外尺寸，box里面margin-box的尺寸
- definite size： 确定尺寸，无需执行布局即可确定的尺寸，如长度、文本尺寸、百分比、初始化包含块的大小或者其他公式
- indefinite size： 不确定的尺寸，在无限的可用空间中尺寸也是无限的。
- avaliable space： 可见空间，表示box放置的空间的大小，该大小由其参与的格式化上下文的规则确定。box可用的空间通常是其包含块的度量（如果是确定的）或无穷大（如果是不确定的）。可用空间可以是最小内容约束或最大内容约束，这会迫使放置在其中的box在该约束下布置。
- stretch fit： 拉伸适应，适配给定尺寸的拉伸，在给定的维度上需要减去边距、边框、填充
- fallback size： 某些尺寸算法在无限的情况下不能很好的计算，需要用后备方案inital containing block来替代

#### Auto Box Sizes
- stretch-fit size
	- 在确定尺寸的可见空间里，填充了指定的轴的尺寸
	- stretch-fit inline size： 在行级模式下，被称为空间宽度(avaliable width)
	- streteh-fit block size
- max-content size
	- 在指定的无限可见空间里，box在指定轴上的理想尺寸，在保证不溢出的情况，尽可能的减少未填充空间，围绕其内容装箱的尺寸
	- max-content inline size： 被称为preferred width和maixmum cell width
	- max-content block size： 在布局后就是内容的块大小
- min-content size
	- box可以采用的最小尺寸不会导致溢出，可以通过选择更大的尺寸来避免
	- min-content inline size：box可以采用的最窄内联尺寸不会导致内联尺寸溢出，也可以通过选择较大的内联尺寸来避免。
	- min-content block size：在块容器、表格、行盒子里，等于max-content block size
- fit-content size
	- 如果在给定轴上的可见空间是确定的，就等于clamp(min-content size, stretch-fit size, max-content-size)，其他情况等于max-content size
	- fit-content inline-size
	- fit-content block-size
- intrinsic size
	- max-content size或者min-content size，主要由内容大小引起的尺寸

## 指定Box的尺寸
#### 尺寸属性
- width：auto | <length-percentage> | min-content | max-content | fit-content(<length-percentage>)
- height：同上
- min-width：同上
- min-height：同上
- max-width：同上
- min-height：同上

#### 尺寸值
- length-percentage：用长度或者百分比来指定尺寸，百分比的计算依赖特定的布局模式
- auto：自动计算尺寸，依赖布局模式
- none：没有限制
- min-content：最小内容的适合长度，仅用于块级
- max-conent：最大内容的适合长度，仅用于块级
- fit-content()：指定一个值，如果值在min-content和max-content之间就使用该值，超出后取边界，用于网格布局

#### box-sizing属性
- content-box：不包含margin、border、padding
- border-box：包含border、padding

#### column-width属性
- min-content：将最佳列宽指定为多列容器内容的最小内容内联大小。
- max-content：将最佳列宽指定为多列容器内容的最大内容内联大小。
- fit-content(<length-percentage>)：min(max-content, max(min-content, <length-percentage>))

## Extirnic Size Determination
> 外在大小调整基于元素的上下文确定大小，而不考虑其内容。

- Percentage Sizing

## Intrinsic Size Determination
> 本质上的大小根据元素的内容确定大小，而不考虑其上下文。
