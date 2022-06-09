# Grid

元素的窗口网格。 Grid 仅根据当前的水平和垂直滚动位置渲染填充自身所需的单元格

文档：https://github.com/bvaughn/react-virtualized/blob/master/docs/Grid.md

```tsx
// 子元素位置
type CellPosition = {
	columnIndex: number; // 列索引
	rowIndex: number; // 行索引
};
type MeasuredCellParent = {
	invalidateCellSizeAfterRender?: (cell: CellPosition) => void;
	recomputeGridSize?: (cell: CellPosition) => void;
};
interface CellMeasurerCacheInterface {
	hasFixedWidth(): boolean;
	hasFixedHeight(): boolean;
	has(rowIndex: number, columnIndex: number): boolean;
	set(rowIndex: number, columnIndex: number, width: number, height: number): void;
	getHeight(rowIndex: number, columnIndex?: number): number;
	getWidth(rowIndex: number, columnIndex?: number): number;
};
// 子元素计算缓存
interface CellMeasurerCache extends CellMeasurerCacheInterface {
    new(params?: CellMeasurerCacheParams);
    clear(rowIndex: number, columnIndex: number): void;
    clearAll(): void;
    columnWidth: (params: { index: number }) => number;
    readonly defaultHeight: number;
    readonly defaultWidth: number;
    rowHeight: (params: { index: number }) => number;
};
type ScrollParams = {
	clientHeight: number;
  clientWidth: number;
  scrollHeight: number;
  scrollLeft: number;
  scrollTop: number;
  scrollWidth: number;
};
// 子元素尺寸和位置管理器类型
type CellSizeAndPositionManager = {
    areOffsetsAdjusted(): boolean;
    configure({ cellCount, estimatedCellSize }: ConfigureParams): void;
    getCellCount(): number;
    getEstimatedCellSize(): number;
    getLastMeasuredIndex(): number;
    getOffsetAdjustment({ containerSize, offset /*safe*/ }: ContainerSizeAndOffset): number;
    getSizeAndPositionOfCell(index: number): SizeAndPositionData;
    getSizeAndPositionOfLastMeasuredCell(): SizeAndPositionData;
    getTotalSize(): number;
    getUpdatedOffsetForIndex(params: {
        align: string;
        containerSize: number;
        currentOffset: number;
        targetIndex: number;
    }): number;
    getVisibleCellRange(params: GetVisibleCellRangeParams): VisibleCellRange;
    resetCell(index: number): void;
};
// 子元素分组渲染属性类型
type GridCellRangeProps = {
	cellCache: Map<any>; // 子元素缓存
	cellRenderer: (props: GridCellProps) => React.ReactNode; // 子元素渲染函数
	columnSizeAndPositionManager: CellSizeAndPositionManager; // 列子元素尺寸和位置管理器
	columnStartIndex: number; // 列开始索引
  columnStopIndex: number; // 列结束索引
  isScrolling: boolean; //
  isScrollingOptOut: boolean; //
  rowSizeAndPositionManager: CellSizeAndPositionManager; // 行子元素尺寸和位置管理器
  rowStartIndex: number; // 行开始索引
  rowStopIndex: number; // 行结束索引
  scrollLeft: number; // 横向偏移量
  scrollTop: number; // 纵向偏移量
  deferredMeasurementCache: CellMeasurerCache;
  horizontalOffsetAdjustment: number;
  parent: React.Component<GridCoreProps> & MeasuredCellParent;
  styleCache: Map<React.CSSProperties>;
  verticalOffsetAdjustment: number;
  visibleColumnIndices: VisibleCellRange;
  visibleRowIndices: VisibleCellRange; 
};
type GridCellProps = {
	columnIndex: number;
  isScrolling: boolean;
  isVisible: boolean;
  key: string;
  parent: React.Component<GridCoreProps> & MeasuredCellParent;
  rowIndex: number;
  style: React.CSSProperties;
};
type GridCoreProps = {
  rowHeight: number | ((params: Index) => number); // 行高
  rowCount: number; // 行数
  width: number; // 网格宽度
  id?: string; 
  isScrolling?: boolean;
  style?: React.CSSProperties; // 样式对象
  className?: string; // 样式名
  noContentRenderer?: () => React.ReactNode; // 无内容渲染函数
  autoContainerWidth?: boolean; // 容器自动宽度
  autoHeight?: boolean; // 自动高度
  autoWidth?: boolean; // 自动宽度
  cellRangeRenderer?: (params: GridCellRangeProps) => React.ReactNode[]; // 负责在给定索引范围的情况下渲染一组单元格。
  containerProps?: object;
  containerRole?: string;
  containerStyle?: React.CSSProperties;
  deferredMeasurementCache?: CellMeasurerCacheInterface;
  estimatedColumnSize?: number;
  estimatedRowSize?: number;
  overscanColumnCount?: number;
  overscanIndicesGetter?: OverscanIndicesGetter;
  overscanRowCount?: number;
  role?: string;
  scrollingResetTimeInterval?: number;
  scrollLeft?: number;
  scrollToAlignment?: Alignment;
  scrollToColumn?: number;
  scrollTop?: number;
  scrollToRow?: number;
  tabIndex?: number | null;
  getScrollbarSize?: () => number;
  onScroll?: (params: ScrollParams) => any;
  onScrollbarPresenceChange?: (params: ScrollbarPresenceParams) => any;
  onSectionRendered?: (params: SectionRenderedParams) => any;
}
type GridProps = {
  cellRenderer: (props: GridCellProps) => React.ReactNode;
  columnCount: number;
  columnWidth: number | ((params: Index) => number);
}
<Grid
	cellRenderer={cellRenderer}
	columnCount={columnCount}
	columnWidth={100}
	height={300}
	rowCount={rowCount}
	rowHeight={30}
	width={300}
/>,
```