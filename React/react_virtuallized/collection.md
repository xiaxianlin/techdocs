# Collection

渲染分散或非线性数据。与呈现网格数据的 Grid 不同，Collection 可以呈现任意定位甚至重叠的数据。

文档：https://github.com/bvaughn/react-virtualized/blob/master/docs/Collection.md

```tsx
type SizeInfo = {
	height: number;
  width: number;
};
type Index = {
  index: number;
}
type ScrollPosition = {
  scrollLeft: number;
	scrollTop: number;
};
// 集合子元素尺寸和位置
type CollectionCellSizeAndPosition = {
  height: number;
  width: number;
  x: number;
  y: number;
};
// 集合子元素渲染参数
type CollectionCellRendererParams = {
  index: number;
  isScrolling: boolean;
  key: number;
  style: CSSProperties;
};
// 集合子元素分组渲染参数
type CollectionCellGroupRendererParams = {
  indices: number[];
  cellSizeAndPositionGetter: (params: Index) => CollectionCellSizeAndPosition;  
  cellRenderer: (params: CollectionCellRendererParams) => React.ReactNode;
};
type CollectionProps = {
  width: number; // 可视宽度
  height: number; // 可视高度
  cellCount: number; // 子元素总数
  cellRenderer: (params: CollectionCellRendererParams) => React.ReactNode; // 子元素渲染函数
  cellSizeAndPositionGetter: (params: Index) => CollectionCellSizeAndPosition; // 子元素位置生成函数
  id?: string; // 自定义集合元素id
  className?: string; // 样式名
  style?: CSSProperties; // 样式
  autoHeight?: boolean; // 结合WindowScroller一起使用
  cellGroupRenderer?: (params: CollectionCellGroupRendererParams) => React.ReactNode[]; // 子元素分组渲染函数
  verticalOverscanSize?: number; // 超过集合的横向空间
  horizontalOverscanSize?: number; // 超出集合的纵向空间
  scrollTop?: number; // 纵向偏移量
  scrollLeft?: number; // 横向偏移量
  scrollToAlignment?: 'auto' | 'end' | 'start' | 'center'; // 滚动指定位置
  scrollToCell?: number; // 滚动到指定索引的子元素
  sectionSize?: number; // 集合片段大小
  noContentRenderer?: () => JSX.Element; // 空集合的内容渲染
  onScroll?: (params: ScrollParams) => any; // 滚动的回调函数
  onSectionRendered?: (params: { indices: Array<number> }) => any; // 集合片段渲染后的回调函数
}
class Collection extends PureComponent<CollectionProps> {
  recomputeCellSizesAndPositions(): void; // 重新计算子元素的尺寸和位置
  calculateSizeAndPositionData(): void;
  getLastRenderedIndices(): number[]; // 获取最后一次渲染的子元素索引
  getTotalSize(): SizeInfo; // 获取总的尺寸
  getScrollPositionForCell(params: {
		align: 'auto' | 'start' | 'end' | 'center';
    width: number;
		cellIndex: number;	
    height: number;
		scrollLeft: number;
		scrollTop: number;
	}): ScrollPosition;
  cellRenderers( params: { isScrolling: boolean; } & SizeInfo ): React.ReactNode[];
}
<Collection
  cellCount={list.length}
  cellRenderer={cellRenderer}
  cellSizeAndPositionGetter={cellSizeAndPositionGetter}
  height={300}
  width={300}
/>
```