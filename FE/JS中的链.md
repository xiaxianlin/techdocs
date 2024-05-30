在JavaScript中有原型链和作用域链这两个概念，这只是自下而上的观看。如果自上而下的看得就会变成树，也就是用于解释JavaScript的抽象语法树（AST）。对于新手来说，两链一树实在过于抽象，以致于经常理解得模棱两可，但是如果能够透彻的理解的两链一树，那么其他概念就迎刃而解，例如继承，封装，闭包等。本文着重会讲述两链，两链都是**单向链结构**(如下图)，只能向上追溯。

\[\[15080673044212.jpg\]\]

### 原型链

在说原型之前，我们先要了解一下类型，在《The ECMAScript Language Specification》的第八章（Types）定义了六种类型，分别是undefined、null、boolean、string、number、object。一般会使用typeof进行类型检查，但是这里有些特殊的情况，我们来看看下面一段代码：

```javascript
console.log(typeof null) // object
console.log(null instanceof Object) // false

function foo(){}
console.log(typeof foo) // function
console.log(foo instanceof Object) // true
```

从上面的这段代码会让我们很疑惑，实际上null是一个单独的类型，而typeof null的结果是一个bug，这个bug官方也是给予了承认，但是由于历史原因，这个bug不会被修复。再来看function，因为函数在进行构造函数检测的时候能够追溯到Object的，所以函数为object类型。至于为什么函数在进行类型检查的时候返回function，可能是因为函数作为第一等公民的存在原因，不过这只是我的猜测。

再回到原型上来，原型只存在于对象之中，而函数具有原型，这也是函数为什么属于对象而不是单独类型的原因。对象会被存储在内存某个地方，而变量保存的是这个对象的引用，引用就像一根线，连接变量于内存中的实例，这个引用你可以理解为寻址。引用是一个很关键的点，不管是原型链还是作用域链都非常重要。再看看下面的代码：

```javascript
var obj1 = { }
obj1.a = 1
var obj2 = Object.create(obj1)
obj2.b = 2
var obj3 = Object.create(obj2)
obj3.c = 3
```

上面代码通过Object.create方法来创建对象从而实现原型继承，这里要注意一点，除了函数有prototype这个属性外，对象变量是没有这个属性，对象变量的有\_*proto*\_属性，这个是属于对象变量的原型属性。函数在被构造的时候会把prototype赋值给\_*proto*\_，但是这两个指向的是同一个对象。接下来用图片来看看3个对象的关系：

![15080718081075-4786514.jpg](asset://localhost/%2FUsers%2Fbytedance%2Fdocs%2F.attachments%2F15080718081075-4786514.jpg)

上图就是一个**原型链**，图中虚线圆圈表示对象的\_*proto*\_属性，一个对象的原型指向另外一个对象，如此往复，直到最终指向Object.prototype为止，而这里的指向是**引用关系**。我们在进行属性调用的时候，其实就是一个寻找属性的过程，从被调用的对象开始，通过原型链一个接一个的寻找，直到找到为止，这个过程是**短路操作**。如果我们需要调用obj3.a，首先会在obj3上寻找a，如果没有，就会通过原型去obj2上寻找a，然后用相同的方式一直找，找到就中止，如果到Object.prototype也没有找到，那么就返回undefined给a。这就是**原型链属性查找**。

说完原型链，再来说说原型链继承，继承概念来源于OOP，通过子类继承父类来实现属性公用。由于JavaScript并没有类这个概念，即便在ES6给出了class这个语法，也依然只是语法糖，包括new操作也只是JavaScript对OOP的一种妥协。在OOP中也只有类具备继承，而对象是不行的。原型链继承是因为原型链属性查找的原因实现了类似于继承这样的一个表象，通过原型链属性查找来实现了属性的公用。在这里要理清下面两点：

1. 属性并没有被继承下来，可以通过Object.hasOwnProperty来检查。
2. 相同属性不是覆盖，而是查找短路。\
   由于原型链的指向是引用关系，因此直接修改原型的是非常危险的事情，就如下面这段代码：

```javascript
console.log(obj3.a) // 1
console.log(obj3.b) // 2
obj2.a = 4
obj2.b = 5
console.log(obj3.a) // 4
console.log(obj3.b) // 5
```

可以发现原来的obj3.a = 1和obj3.b = 2全部被改掉了，这在实际工作中是非常危险的事情，由于原型被修改导致的问题很难被追查。所以JavaScript中的继承一般使用Object.assign和对象属性一一拷贝的方式来实现，这样可以隔离大部分引用，但是面对对象嵌套对象有时候就很无力，除非用递归来是实现层层拷贝，但是这样的消耗也是很恐怖的。

### 作用域链

理解完原型链后再理解作用域就会轻松很多，在这里把作用域限定函数作用域和全局作用域，可以把函数作用域比作对象，把父级函数作用域比作原型，把全局作用域比作Object.prototype，这样是不是就清晰明了。\
看图说话：

![15080753470431.jpg](asset://localhost/%2FUsers%2Fbytedance%2Fdocs%2F.attachments%2F15080753470431.jpg)

用代码来表示：

```javascript
var globalScope = Object.prototype
globalScope.a = 1

var aooScope = Object.create(globalScope)
aooScope.b = 2

var booScope = Object.create(aooScope)
booScope.c = 3

var cooScope = Object.create(booScope)
cooScope.d = 4
```

这样看是不是就很有意思多了，每个作用域相当于一个对象，里面的变量都是这个对象的属性，父级作用域相当于是子级作用域的原型，通过原型链属性查询，子级作用域可以访问父级作用域的属性，这个查找也基本短路属性，一级一级往上找，找到就停。还有一种说法是复制并写入，就是子级把父级的变量拷贝一份并合并给自己，在我看来，这明显不可取，如果修改父级变量，子级访问的肯定是修改的值。接下来了解下跟作用域息息相关的闭包，在YDKJS里面是这样定义闭包的：

> Closure is when a function can remember and access its lexical scope even when it's invoked outside its lexical scope.

这里就不去关注remember和access，如果从作用域链的角度出发，这两点就是基本条件，关注点集中到invoked outside，外部调用是啥意思呢，拿图中的代码来说就是在全局里面调用coo方法，这个怎么来调用就看下面的两段代码：\
代码1：

```javascript
var a = 1
var fn
function aoo() {
    var b = 2
    function boo() {
        var c = 3
        function coo() {
            var d = 4
            // todos
            console.log(b)
        }
        fn = coo
    }
    boo()
}
aoo()
fn() // 2
```

代码2：

```javascript
var a = 1
function aoo() {
    var b = 2
    function boo() {
        var c = 3
        function coo() {
            var d = 4
            // todos
            console.log(b)
        }
        return coo
    }
    return boo
}
var fn = aoo()()
fn() // 2
```

两段代码都是现实了外部调用，不管是代码1的直接赋值还是代码2的返回赋值，结果都是fn引用了coo，用于引用是在外部调用，所以coo的作用域相对外部来说是封闭的，所以也就叫闭包。那么在回到刚才写的作用域链里面，fn引用了coo，coo的作用域对象是cooScope，这样就很清晰的看到fn在调用的时候是coo是如何访问父级作用的变量的。

### 结束语

我采用经济学里常用的方法，去掉大部分变量，通过对简单的变量分析来研究，因此这些解释有点粗糙，不过在我看来还是比较通俗易懂的。作用域还有很多内容，闭包也有其他的内容，但是这些都不是这里讨论。