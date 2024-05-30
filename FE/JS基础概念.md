### 宏任务与微任务

> ES6 规范中，microtask 称为 jobs，macrotask 称为 task，宏任务是由宿主发起的，而微任务由JavaScript自身发起。\
> 在微任务没有执行完成时，不会去执行宏任务。

- 微任务：nextTick、MutationObserver、Promise
- 宏任务：I/O、setTimeout、setInterval、setImmediate、requestAnimationFrame、DomEvent