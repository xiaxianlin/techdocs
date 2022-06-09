# 模块化以及JS中的实践
## 模块化随想
在产品的发展中，一个产品往往会从简单走向复杂，这时候就需要对产品进行解构，通过分解再组合的方式实现解耦，而分解出来的部分就是模块。因此可以看出模块第一个特性就是复用，但不是所有模块都需要被复用。在设计产品时，往往会将一些相似或相关的独立功能抽取出来成为一个独立模块，相关指的是这一类功能操作对象有相关性，这一类模块常被称为功能模块，这类模块有极高的独立性，一般不会有其他依赖，因此这类模块只要有良好的设计规范，可以极好的进行模块替换，这就带来了模块设计中的可维护性。而优秀的模块封装具有高度的内聚，模块使用者不需要知道模块的实现，只需要提供明确的接口即可，当然优秀的模块需要提供良好的可扩展性，提供使用者可以自己定制功能的能力。

从上述提到的复用、解耦、可维护性和可扩展性可以看出模块其实也一种系统，这是这种系统比较微小，在后端中微服务也是模块化设计的一类实现。从现实中找模块化的实现非乐高莫属，乐高的积木提供几个基础模型，这几个基础模型就是功能模块。将多个基础模型用特有的规则组成各种子模型，通过对子模型的组合构成完整模型，这类完整模型就是对现实物品的抽象，将完整模型继续组合成场景。程序世界里面的系统也是如此，在程序设计中，一般会有功能模块，通用业务模块，完整业务模块，子系统模块以及系统模块等等，都是从最基础的模块一层一层的组合而成，这种组合方式带来了本段开头提到的好处。

在程序设计中，如果能在最初确定规模以及复杂度，那么模块设计应该在开发之前的设计文档中体现，不要求尽善尽美，但是一定满足核心需求，然后在开发中不断提炼、重构。不能确定规模以及复杂度，只能在开发中完善，而这类完善是写出优质代码和稳定业务的基石，不可因为繁琐就放弃。在长期的模块提取和重构中，可以锻炼出非常优秀的设计能力。在进行模块设计的时候可以参考设计模式的六大原则。

模块化在社会发展中的体现应该就是社会化分工，分工后的专业程度非常高，实现效率也非常高。而程序中也是这样，超大型的系统往往由各种子系统组合，各个子系统由更加专业的开发人员开发会带来更高的质量和效率，只需要有优秀的接口设计就可以完美的进行系统间的组装。

## JS中的模块化技术
在前端开发的历史中，SPA页面最早并不是很多，所以那时候更多的是一个页面对应一个专属JS文件，然后在引用几个通用文件，这里可以把每个文件认成一个模块。再加上那时候的业务并不复杂，大部分业务逻辑也是由服务端完成，所以JS代码量并不大，这个时候模块化的需求并不高，随着浏览器的能力越来越高，业务逻辑逐步从服务端转移到客户端，代码量也越来越大，因此带来了代码拆分、全局变量冲突等问题，由此模块化的需求越来越高。最早的模块化应用是在浏览器之外的，随着SPA的崛起，打包工具的发展，浏览器的模块化才开始渐渐兴起。

### CommonJS

> What I’m describing here is not a technical problem. It’s a matter of people getting together and making a decision to step forward and start building up something bigger and cooler together.

由Mozilla的工程师 Kevin Dangoor在2009年1月成立的项目，最初的名称叫ServerJS，后来在童年8月份改名为CommonJS。该规范不属于ECMA的TC39的工作，但是有部分TC39的成员参与。

> These modules are offered privacy of their top scope, facility for importing singleton objects from other modules, and exporting their own API. By implication, this specification defines the minimum features that a module system must provide in order to support interoperable modules.

以上内容来自CommonJS的官方文档，主要的关键点只有私有的顶级作用域、简单的导入其他模块的单个对象以及提供自己的API。

**Modules/1.0**
- 在一个模块中有一个全局变量require的函数，该函数接收一个模块的标识名，返回另外一个模块提供的API。
- 在一个模块中有一个全局变量exports的对象，可以在这个对象添加对外的API。
- 模块只能使用exports来对外提供访问入口。

**Modules/1.1**
- 在一个模块中，必须有一个全局变量module的对象，这个对象有一个只读且不能删除的属性id，同时提供了一个uri的属性。

main.js
```javascript
exports.add = function() {
    var sum = 0, i = 0, args = arguments, l = args.length;
    while (i < l) {
        sum += args[i++];
    }
    return sum;
};
```

increment.js

```javascript
var add = require('math').add;
exports.increment = function(val) {
    return add(val, 1);
};
```

program.js

```javascript
var inc = require('increment').increment;
var a = 1;
inc(a); // 2
 
module.id == "program";
```

从上述规范和代码示例来看，CommonJS不是很适合浏览器端的开发。

### AMD(Asynchronous Module Definition)

顾名思义，AMD提供的是一种异步模块加载的方案。在浏览器端的开发，进行页面优化的一项就是减少文件请求和减小文件的大小，而异步加载加载很好的解决了这一问题，实现了按需加载。AMD只提供了一个全局函数define。

```javascript
define(id?, dependencies?, factory);
```

- id: 模块的标识，可以选择不填，是其他模块进行依赖加载的唯一标识。
- dependencies: 标识数组，根据标识加载模块，并传入到factory函数中。
- factory: 处理模块的业务，接收加载的依赖，依赖的顺序跟dependencies顺序一致，函数可以通过exports导出模块，或者直接使用return导出模块。

**示例**
```javascript
define("alpha", ["require", "exports", "beta"], function (require, exports, beta) 
{
    exports.verb = function() {
        return beta.verb();
        //Or:
        return require("beta").verb();
    }
});
```

        在这里只是简单的介绍下AMD的规范和使用，AMD在规范实现了对CommonJS上的兼容，但这种兼容有点不伦不类。AMD不能兼容服务端的IO和文件系统等特性。前端对AMD进行实现的库最主要有require.js等

### UMD(Universal Module Definition)

       UMD并不是一种规范，而是规范的适配器，通过UMD可以实现对AMD和CommonJS的同时支持。因此UMD模块可以同时运行客户端和服务端，现在大部分库都是使用UMD，主要规范多了就蛋疼。

**示例**
```javascript
(function (root, factory) {
  if (typeof define === 'function' && define.amd) {
      // AMD
    define(['myModule', 'myOtherModule'], factory);
  } else if (typeof exports === 'object') {
      // CommonJS
    module.exports = factory(require('myModule'), require('myOtherModule'));
  } else {
    // Browser globals (Note: root is window)
    root.returnExports = factory(root.myModule, root.myOtherModule);
  }
}(this, function (myModule, myOtherModule) {
  // Methods
  function notHelloOrGoodbye(){}; // A private method
  function hello(){}; // A public method because it's returned (see below)
  function goodbye(){}; // A public method because it's returned (see below)

  // Exposed public methods
  return {
      hello: hello,
      goodbye: goodbye
  }
}));
```

### ES6的Module

在最新的ES6标准，给了完整的模块的规范，一个文件代表一个模块，模块内部默认使用严格模式。模块内的变量均为本地变量。可以通过\<\script type=“module” src=“filename”\>\导入模块。

- 模块内通过export提供属性的对外访问，通过export default提供默认属性，export default只能有一个且只能是表达式，函数或者类才能用default。
- 模块内通过import提供对其他的访问，import只能放在文件的头部。
- 每个模块都是一个单例

lib.js

```javascript
const name = 'lib'
const sqrt = Math.sqrt;
export function square(x) {
    return x * x;
}
export function diag(x, y) {
    return sqrt(square(x) + square(y));
} 
export default name
```

main.js

```javascript
import name, { square, diag } from 'lib';
console.log(name); // 'lib'
console.log(square(11)); // 121
console.log(diag(4, 3)); // 5
```

这里只是简单的介绍ES6模块的规范内容，至于其他的很多机制不详细展开，具体去参考相关文档，由于ES6的规范在大部分浏览器上并未实现，所以一般采用babel之内的进行翻译，翻译成CommonJS之类的规范。

### CMD(Common Module Definition)

CMD是SeaJS根据AMD进行的一次扩展，提供了模块按需延迟加载的功能。

## 组件化
组件化是前端对模块化的另一种实现，主要是通过对业务或者功能进行封装，这个封装包含了HTML结构，CSS样式以及JS交互，完成一个开封即用的模块。

## 结语
模块化重要的不是规范，而是思想，是对具体业务、具体场景进行不断解构、重组后获得的宝贵经验。所有优秀的设计都是从简单到复杂不断的推演出来的，而模块化设计也是一样。
