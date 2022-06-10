# React Event

在v17之后移除了事件池的优化方案。

### 数据结构

```ts
interface Event{
	bubbles:boolean;
 	cancelable:boolean;
 	currentTarget:DOMEventTarget;
 	defaultPrevented:boolean;
 	eventPhase:number;
 	isTrusted:boolean;
 	nativeEvent:DOMEvent;
 	preventDefault():void;
 	isDefaultPrevented():boolean;
 	stopPropagation():void;
 	isPropagationStopped():boolean;
 	persist():void; // 在v17里面该方法已经失效
 	target:DOMEventTarget;
 	timeStamp:number;
 	type:string;
}
```



### 事件分类

- Clipboard Events
    - onCopy
    - onCut
    - onPaste
- Composition Events
    - onCompositionStart
    - onCompositionEnd
    - onCompostionUpdate
- Keyboard Events
    - onKeyDown
    - onKeyPress
    - onKeyUp
- Focus Events
    - onFocus
    - onBlur
- Form Events
    - onChange
    - onInput
    - onInvalid
    - onReset
    - onSubmit
- Generic Events
    - onError
    - onLoad
- Mouse Events
    - onClick
    - onContextMenu
    - onDoubleClick
    - onDrag
    - onDragEnd
    - onDragEnter
    - onDragExit
    - onDragLeave
    - onDragOver
    - onDragStart
    - onDrop
    - onMouseDown
    - onMouseEnter
    - onMouseLeave
    - onMouseMove
    - onMouseOut
    - onMouseOver
    - onMouseUp
- Pointer Events
    - onPointerDown
    - onPointerMove
    - onPointerUp
    - onPointerCancel
    - onGotPointerCapture
    - onLostPointerCapture
    - onPointerEnter
    - onPointerLeave
    - onPointerOver
    - onPointerOut
- Selection Events
    - onSelect
- Touch Events
    - onTouchCancel
    - onTouchEnd
    - onTouchMove
    - onTouchStart
- UI Events
    - onScroll
- Wheel Events
    - onWheel
- Meida Events
    - onAbort
    - onCanPlay
    - onCanPlayThrough
    - onDurationChange
    - onEmptied
    - onEncrypted
    - onEnded
    - onError
    - onLoadedData
    - onLoadedMetadata
    - onLoadStart
    - onPause
    - onPlay
    - onPlaying
    - onProgress
    - onRateChange
    - onSeeked
    - onSeeking
    - onStalled
    - onSuspend
    - onTimeUpdate
    - onVolumeChange
    - onWaiting
- Image Events
    - onLoad
    - onError
- Animation Events
    - onAnimationStart
    - onAnimationEnd
    - onAnimationIteration
- Transition Events
    - onTransitionEnd
- Other Events
    - onToggle