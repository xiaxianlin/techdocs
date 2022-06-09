# React-Window

react-window提供了4个组件，分别是**FixedSizeList**、**VariableSizeList**、**FixedSizeGrid**、**VariableSizeGrid**。

react-window的dom结构由外容器、内容器和列表子元素组成。

```jsx
<OuterElement>
  <InnerElement>
  	<ListItem />
    <ListItem />
    <ListItem />
  </InnerElement>
</OuterElement>
```

##### 通用属性

```ts
type ReactElementType = FunctionComponent<any> | ComponentClass<any> | string;
interface CommonProps<T = any>{
  itemData?: T; // 子项数据
  className?: string; // 外容器样式名
  style?: CSSProperties; // 外容器样式
  outerElementType?: ReactElementType; // 外容器元素类型
  outerRef?: Ref<any>; // 外容器元素引用
  innerElementType?: ReactElementType; // 内容器元素类型
  innerRef?: Ref<any>; // 内容器元素引用
  useIsScrolling?: boolean; // 开启后会传递 isScrolling 参数给子元素渲染函数，如果滚动加载过 isScrolling 为 true
}
```

##### 列表属性

```ts
type ListItemKeySelector<T = any> = (index: number, data: T) => string | number;
interface ListChildComponentProps<T = any> {
  index: number; // 子元素索引
  style: CSSProperties; // 子元素样式
  data: T; // 子元素数据，来源于 itemData
  isScrolling?: boolean; // 当useIsScrolling开始时，会传入true或false
}
interface ListOnItemsRenderedProps {
 	overscanStartIndex: number;
  overscanStopIndex: number;
  visibleStartIndex: number;
  visibleStopIndex: number;
}
interface ListOnScrollProps {
	scrollDirection: "forward" | "backward";
  scrollOffset: number;
  scrollUpdateWasRequested: boolean;
}
interface ListProps<T = any> extends CommonProps<T>{
  children: ComponentType<ListChildComponentProps<T>>; // 子元素渲染函数
  width: number | string; // 列表宽度
  height: number | string; // 列表高度
  itemCount: number; // 子元素总数
  direction?: CSSDirection | "vertical" | "horizontal"; // 渲染方向
  layout?: Layout;
  initialScrollOffset?: number;
  itemKey?: ListItemKeySelector<T>;
  overscanCount?: number;
  onItemsRendered?: (props: ListOnItemsRenderedProps) => any;
  onScroll?: (props: ListOnScrollProps) => any; // 滚动回调函数
}
```

##### 表格属性

```ts
type GridItemKeySelector<T = any> = (params: {
	columnIndex: number;
  rowIndex: number;
  data: T;
}) => Key;
interface GridChildComponentProps<T = any> {
  columnIndex: number;
  rowIndex: number;
  style: CSSProperties;
  data: T;
  isScrolling?: boolean | undefined;
}
interface GridOnItemsRenderedProps {
	overscanColumnStartIndex: number;
  overscanColumnStopIndex: number;
  overscanRowStartIndex: number;
  overscanRowStopIndex: number;
  visibleColumnStartIndex: number;
  visibleColumnStopIndex: number;
  visibleRowStartIndex: number;
  visibleRowStopIndex: number;
}
interface GridOnScrollProps {
  horizontalScrollDirection: ScrollDirection;
  scrollLeft: number;
  scrollTop: number;
  scrollUpdateWasRequested: boolean;
  verticalScrollDirection: ScrollDirection;
}
interface GridProps<T = any> extends CommonProps<T>{
  columnCount: number;
  direction?: CSSDirection;
  height: number;
  initialScrollLeft?: number;
  initialScrollTop?: number;
  itemKey?: GridItemKeySelector<T>;
  onItemsRendered?: (props: GridOnItemsRenderedProps) => any;
  onScroll?: (props: GridOnScrollProps) => any;
  overscanColumnCount?: number;
  overscanRowCount?: number;
  rowCount: number;
  width: number;
}
```

##### \<FixedSizeList>

固定大小列表，必须给定**width**、**height**、**itemCount**、**itemSize**四个属性

```tsx
interface FixedSizeListProps<T = any> extends ListProps<T> {
  itemSize: number; // 子元素的高度
}
class FixedSizeList<T = any> extends Component<FixedSizeListProps<T>> {
  scrollTo(scrollOffset: number): void; // 滚动的指定高度位置
  scrollToItem(index: number, align?: Align): void; // 滚动到指定子元素位置
}
<FiexedSizeList {...props}>
  {({index, style}) => (
  	<div style={style}>Item {index}</div>
  )}
</FiexedSizeList>
```

##### \<VariableSizeList>

在FixedSizeList组件的参数基础上做了一些修改和新增，可以根据索引自定义子元素的高度。

```tsx
interface VariableSizeListProps<T = any> extends ListProps<T> {
  itemSize: (index: number) => number; // 子元素的高度生成函数
  estimatedItemSize?: number; // 子元素在窗口方向上的估计大小。垂直列表是行高。水平列表是列宽。
}
class VariableSizeList<T = any> extends Component<FixedSizeListProps<T>> {
  scrollTo(scrollOffset: number): void; // 滚动的指定高度位置
  scrollToItem(index: number, align?: Align): void; // 滚动到指定子元素位置
  resetAfterIndex(index: number, shouldForceUpdate?: boolean): void; // 当一个元素高度发生变化后，调用该方法清除指定元素之后的缓存数据
}
<VariableSizeList {...props}>
  {({index, style}) => (
  	<div style={style}>Item {index}</div>
  )}
</VariableSizeList>
```

##### \<FixedSizeGrid>

固定大小网格，需指定**width**、**height**、**columnCount**、**columWith**、**rowCount**、**rowHeight**属性。

```tsx
interface FixedSizeGridProps<T = any> extends GridProps<T> {
  columnWidth: number; // 列宽
  rowHeight: number; // 行高
}
class FixedSizeGrid<T = any> extends Component<FixedSizeGridProps<T>> {
  scrollTo(scrollOffset: number): void; // 滚动的指定高度位置
  scrollToItem(params: {
    align?: Align | undefined;
    columnIndex?: number | undefined;
    rowIndex?: number | undefined;
  }): void; // 滚动到指定子元素位置
}
<FixedSizeGrid {...props}>
  {({ columnIndex, rowIndex, style }) => (
    <div style={style}>
      row {rowIndex}, column {columnIndex}
    </div>
  )}
</FixedSizeGrid>
```

##### \<VariableSizeGrid>

在FixedSizeGrid组件的参数基础上做了一些修改和新增，可以根据索引自定义子元素的高度。

```tsx
interface VariableSizeGridProps<T = any> extends GridProps<T> {
  columnWidth: (index: number) => number;
  rowHeight: (index: number) => number;
  estimatedColumnWidth?: number;
  estimatedRowHeight?: number;
}
class VariableSizeGrid<T = any> extends Component<VariableSizeGridProps<T>> {
  scrollTo(scrollOffset: number): void; // 滚动的指定高度位置
  scrollToItem(params: {
    align?: Align | undefined;
    columnIndex?: number | undefined;
    rowIndex?: number | undefined;
  }): void; // 滚动到指定子元素位置
  resetAfterColumnIndex(index: number, shouldForceUpdate?: boolean): void;
  resetAfterIndices(params: {
    columnIndex: number;
    rowIndex: number;
    shouldForceUpdate?: boolean | undefined;
  }): void;
  resetAfterRowIndex(index: number, shouldForceUpdate?: boolean): void;
}
<VariableSizeGrid {...props}>
  {({ columnIndex, rowIndex, style }) => (
    <div style={style}>
      row {rowIndex}, column {columnIndex}
    </div>
  )}
</VariableSizeGrid>
```

