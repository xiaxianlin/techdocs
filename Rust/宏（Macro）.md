Rust 的功能和语法可以通过称为宏的自定义定义进行扩展。它们被命名，并通过一致的语法调用：`some_extension!(...)`。

### 调用

```rust
// Used as an expression.
let x = vec![1,2,3];

// Used as a statement.
println!("Hello!");

// Used in a pattern.
macro_rules! pat {
    ($i:ident) => (Some($i))
}

if let pat!(x) = Some(1) {
    assert_eq!(x, 1);
}

// Used in a type.
macro_rules! Tuple {
    { $A:ty, $B:ty } => { ($A, $B) };
}

type N2 = Tuple!(i32, i32);

// Used as an item.
use std::cell::RefCell;
thread_local!(static FOO: RefCell<u32> = RefCell::new(1));

// Used as an associated item.
macro_rules! const_maker {
    ($t:ty, $v:tt) => { const CONST: $t = $v; };
}
trait T {
    const_maker!{i32, 7}
}

// Macro calls within macros.
macro_rules! example {
    () => { println!("Macro call in a macro!") };
}
// Outer macro `example` is expanded, then inner macro `println` is expanded.
example!();
```

### 转录

`macro_rules` 允许用户以声明的方式定义语法扩展。我们将此类扩展称为“示例宏”或简称为“宏”。

每个宏都有一个名称和一个或多个规则。每个规则有两个部分：一个**匹配器(matcher)**，描述它匹配的语法，以及一个**转录器(transcriber)**，描述将替换成功匹配的调用的语法。匹配器和转录器都必须用分隔符包围。宏可以扩展到表达式、语句、项目（包括特征、实现和外来项目）、类型或模式。

在匹配器和转录器中，`$` 标记用于调用宏引擎的特殊行为（在下面的元变量和重复中描述）。不属于此类调用的标记将按字面意思进行匹配和转录，但有一个例外。例外是匹配器的外部分隔符将匹配任何一对分隔符。因此，例如，匹配器 `(())` 将匹配 `{()}` 而不是 `{{}}`。字符 `$` 不能按字面匹配或转录。

当将匹配的片段转发到另一个宏时，第二个宏中的匹配器将看到片段类型的不透明 AST。第二个宏不能使用文字标记来匹配匹配器中的片段，只能使用相同类型的片段说明符。 `ident`、`lifetime` 和 `tt` 片段类型是一个例外，可以通过文字标记进行匹配。下面说明了这种限制：

```rust
macro_rules! foo {
    ($l:expr) => { bar!($l); }
// ERROR:               ^^ no rules expected this token in macro call
}

// compiles OK
macro_rules! foo {
    ($l:tt) => { bar!($l); }
}

macro_rules! bar {
    (3) => {}
}

foo!(3);
```

### 元变量

在匹配器中，`$name:fragment-specifier` 匹配指定类型的 Rust 语法片段并将其绑定到元变量 `$name`。

有效片段说明符列表：

- `item` item 是 crate 的组成部分。Items 由一组嵌套的模块组织在一个 crate 中。
- `block` 块级表达式
- `stmt` 没有尾随分号的语句（需要分号的项目语句除外）
- `pat_param` 一个 PattterNoTopAlt (模式用于将值与结构匹配，并可选择将变量绑定到这些结构内的值。它们还用于函数和闭包的变量声明和参数)。
- `pat` 至少是任何 PatternNoTopAlt，可能更多取决于版本
- `expr` 表达式
- `ty` 类型表达式
- `ident` 标识符
- `path` TypePath 样式路径
- `tt` 一个 TokenTree
- `meta` 一个 Attr，一个属性的内容
- `lifetime` 生命周期标识
- `vis` 一个可能为空的可见性限定符
- `literal` 匹配 `-` LiteralExpression

在转录器中，元变量通过 `$name` 简单地引用，因为片段类型在匹配器中指定。元变量被替换为与其匹配的语法元素。关键字元变量 `$crate` 可用于引用当前的 crate，元变量可以被转录不止一次或根本不被转录。

出于向后兼容的原因，虽然 `_` 也是一个表达式，但独立的下划线与 `exp`r 片段说明符不匹配。但是，当 `_` 作为子表达式出现时，它与 `expr` 片段说明符匹配。

### 重复

在匹配器和转录器中，重复是通过将要重复的标记放在 `$(...)` 中来指示的，然后是一个重复运算符，中间可以选择一个分隔符标记。分隔符标记可以是除定界符或重复运算符之一以外的任何标记，但是 `;` 和 `,` 是最常见的。例如，`$( $i:ident ),*` 表示任意数量的用逗号分隔的标识符。允许嵌套重复。

重复操作符：

- `*` — 表示任意次数的重复。
- `+` — 表示任何数字，但至少为一个。
- `?` — 表示出现零次或一次的可选片段。

自从 `?` 最多表示一次，不能与分隔符一起使用。

重复的片段匹配并转录为指定数量的片段，由分隔符标记分隔。元变量与其相应片段的每次重复相匹配。例如，上面的 `$( $i:ident ),*` 示例将 `$i` 匹配到列表中的所有标识符。

在转录期间，额外的限制适用于重复，以便编译器知道如何正确扩展它们：

- 元变量在转录器中的重复次数、种类和嵌套顺序必须与在匹配器中完全相同。所以对于匹配器 `$( $i:ident ),*`，转录器 `=> { $i }`, `=> { $( $( $i)* )* }` 和 `=> { $( $i )+ } `是都是非法的，但是 `=> { $( $i );* }` 是正确的，并且用分号分隔的列表替换了逗号分隔的标识符列表。
- 转录器中的每次重复都必须包含至少一个元变量，以决定将其扩展多少次。如果多个元变量出现在同一个重复中，它们必须绑定到相同数量的片段。例如 `( $( $i:ident ),* ; $( $j:ident ),* ) => (( $( ($i,$j) ),* ))` 必须绑定相同数量的 `$i` 片段作为 `$j` 片段。这意味着用 `(a, b, c; d, e, f)` 调用宏是合法的并且扩展为 `((a,d), (b,e), (c,f))`，但是 `(a, b , c; d, e)` 是非法的，因为它没有相同的数字。此要求适用于嵌套重复的每一层。

### 作用域、导出和导入

由于历史原因，示例宏的作用域并不完全像项目一样工作。宏有两种形式的范围：文本范围和基于路径的范围。文本范围基于事物在源文件中出现的顺序，甚至是跨多个文件的顺序，并且是默认范围。下面将进一步解释。基于路径的作用域与项目作用域的工作方式完全相同。宏的作用域、导出和导入主要由属性控制。

当一个宏被一个非限定标识符（不是多部分路径的一部分）调用时，首先在文本范围内查找它。如果这没有产生任何结果，则在基于路径的范围内查找它。如果宏的名称由路径限定，则仅在基于路径的作用域中查找它。

```rust
use lazy_static::lazy_static; // 基于路径导入

macro_rules! lazy_static { // 文本范围内定义
    (lazy) => {};
}

lazy_static!{lazy} // 首先在文本范围选择
self::lazy_static!{} // 基于路径的查找忽略我们的宏，找到导入的宏。
```

#### 文本作用域

文本范围主要基于事物在源文件中出现的顺序，并且与使用 `let` 声明的局部变量的范围类似，除了它也适用于模块级别。当 `macro_rules!` 用于定义宏，宏在定义之后进入范围（注意它仍然可以递归使用，因为名称是从调用站点查找的），直到其周围的范围，通常是模块，被关闭。这可以进入子模块，甚至跨越多个文件：

```rust
//// src/lib.rs
mod has_macro {
    // m!{} // Error: m is not in scope.
    macro_rules! m {
        () => {};
    }
    m!{} // OK: appears after declaration of m.
    mod uses_macro;
}
// m!{} // Error: m is not in scope.

//// src/has_macro/uses_macro.rs
m!{} // OK: appears after declaration of m in src/lib.rs
```

多次定义宏不是错误；除非超出范围，否则最近的声明将影响前一个声明。

```rust
macro_rules! m {
    (1) => {};
}
m!(1);

mod inner {
    m!(1);
    macro_rules! m {
        (2) => {};
    }
    // m!(1); // Error: no rule matches '1'
    m!(2);
    macro_rules! m {
        (3) => {};
    }
    m!(3);
}
m!(1);
```

宏也可以在函数内部本地声明和使用，并且工作方式类似：

```rust
fn foo() {
    // m!(); // Error: m is not in scope.
    macro_rules! m {
        () => {};
    }
    m!();
}
// m!(); // Error: m is not in scope.
```

#### `macro_user` 属性

`macro_use` 属性有两个用途。首先，它可以用于使模块的宏范围在模块关闭时不会结束，方法是将其应用于模块：

```rust
#[macro_use]
mod inner {
    macro_rules! m {
        () => {};
    }
}
m!();
```

其次，它可用于从另一个 crate 导入宏，方法是将其附加到 crate 根模块中出现的 `extern crate` 声明。以这种方式导入的宏被导入到 `macro_use` 前奏中，而不是以文本形式导入，这意味着它们可以被任何其他名称遮蔽。虽然通过 `#[macro_use]` 导入的宏可以在 `import` 语句之前使用，但如果发生冲突，最后导入的宏胜出。可选地，可以使用 `MetaListIdents` 语法指定要导入的宏列表；当 `#[macro_use]` 应用于模块时，不支持此功能。

```rust
#[macro_use(lazy_static)] // 或者使用 #[macro_use] 导入所有的宏
extern crate lazy_static;
lazy_static!{}
// self::lazy_static!{} // Error: lazy_static is not defined in `self`
```

使用 `#[macro_use]` 导入的宏必须使用 `#[macro_export]` 导出，如下所述。

#### 基于路径的作用域

默认情况下，宏没有基于路径的范围。但是，如果它具有 `#[macro_export]` 属性，那么它会在 crate 根范围内声明，并且可以正常引用如下：

```rust
self::m!();
m!(); // OK: Path-based lookup finds m in the current module.

mod inner {
    super::m!();
    crate::m!();
}

mod mac {
    #[macro_export]
    macro_rules! m {
        () => {};
    }
}
```

标有 `#[macro_export]` 的宏始终是 `pub` 并且可以由其他 crate 引用，通过路径或通过 `#[macro_use]` 如上所述。