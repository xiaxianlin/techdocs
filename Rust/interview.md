# 面试题

#### 1. RwLock 对想要在多线程下正确使用，T的约束是？

类型参数 T 表示该锁保护的数据。要求 T 满足 Send 跨线程共享和 Sync 允许通过 reader 并发访问。

#### 2. 以下代码是否可以通过编译？并说明原因。

```rust
trait A{ fn foo(&self) -> Self; }
Box<Vec<dyn A>>;
```

不可以，参考object safe 三条规则。

-   所有父级 `Trait` 也必须是对象安全的
-   `Sized` 类型对象一定不是 `Supertraits`
-   不能有任何关联的常量
-   所有关联的函数必须要么可以从 trait 对象派发，要么显式不可派发
    -   可派发函数必须条件
        -   没有任何类型参数（尽管允许使用生命周期参数）
        -   是一个不使用 Self 的方法，除了在接收者的类型中
        -   具有以下类型之一的接收器：`&Self`、`&mut Self`、`Box<Self>`、`Rc<Self>`、`Arc<Self>`、`Pin<P>`
        -   没有 `where Self: Sized` 约束
    -   显示不可派发函数必须条件
        -   有 `where Self: Sized` 约束

#### 3. Clone 与 Copy 的区别是什么？

Copy 是 marker trait，告诉编译器需要 move 的时候 copy。Clone 表示拷贝语义，有函数体。不正确的实现 Clone 可能会导致 Copy 出 BUG。

#### 4. Deref的被调用过程

Deref 是一个 trait，由于 rust 在调用的时候会自动加入正确数量的 * 表示解引用。则，即使你不加入 * 也能调用到 Deref。

#### 5. Rust里如何实现在函数入口和出口自动打印一行日志？ 

调用处宏调用、声明时用宏声明包裹、proc_macro 包裹函数、邪道一点用 compiler plugin、llvm 插桩等形式进行。

#### 6. Box<dyn (Fn() + Send +'static)> 是什么意思?

一个可以被Send到其他线程里的没有参数和返回值的 callable 对象，即 Closure，同时是 ownershiped，带有 static 的生命周期，也就说明没有对上下文的引用。