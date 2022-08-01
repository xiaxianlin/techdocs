# 线程

在大部分现代操作系统中，已执行程序的代码在一个 **进程**（*process*）中运行，操作系统则负责管理多个进程。在程序内部，也可以拥有多个同时运行的独立部分。运行这些独立部分的功能被称为 **线程**（*threads*）。

Rust 标准库只提供了 1:1 线程模型实现。由于 Rust 是较为底层的语言，如果你愿意牺牲性能来换取抽象，以获得对线程运行更精细的控制及更低的上下文切换成本，你可以使用实现了 M:N 线程模型的 crate。

### 创建线程

使用 `thread::spawn` 创建一个新线程，并传递一个闭包，其中包含新线程运行的代码。

```rust
use std::thread;
use std::time::Duration;

let handle = thread::spawn(|| {
  for i in 1..10 {
    println!("hi number {} from the spawned thread!", i);
    thread::sleep(Duration::from_millis(1));
  }
});
```

当主线程结束时，新线程也会结束，而不管其是否执行完毕。可以使用 `join` 等待所有线程结束。

```rust
handle.join().unwrap();
```

### 线程与 move 闭包

`move` 闭包经常与 `thread::spawn` 一起使用，因为它允许在一个线程中使用另一个线程的数据。使用 `move` 关键字强制闭包获取其使用的环境值的所有权。

```rust
use std::thread;

fn main() {
  let v = vec![1, 2, 3];

  let handle = thread::spawn(|| {
    println!("Here's a vector: {:?}", v);
  });

  handle.join().unwrap();
}
```

在上面这个示例中，变量 `v` 在线程被使用，但是 rust 无法明确线程执行的时间，所以无法知晓 `v` 的引用是否一直有效。这时候需要使用 `move` 来转移所有权。

```rust
let handle = thread::spawn(move || {
```

### 线程间数据传递

**消息传递**（*message passing*）是一个确保安全并发的方式，线程或 actor 通过发送包含数据的消息来相互沟通。Rust 中一个实现消息传递并发的主要工具是 **通道**（*channel*），Rust 标准库提供了其实现的编程概念。通道有两部分组成，一个发送者（transmitter）和一个接收者（receiver）。代码中的一部分调用发送者的方法以及希望发送的数据，另一部分则检查接收端收到的消息。当发送者或接收者任一被丢弃时可以认为通道被 **关闭**（*closed*）了。

##### 创建通道

使用 `std::sync::mpsc` 模块来创建一个通道，返回一个元组，包含了发送者和接收者。`mpsc` 是 **多个生产者，单个消费者**（*multiple producer, single consumer*）的缩写。Rust 标准库实现通道的方式意味着一个通道可以有多个产生值的 **发送**（*sending*）端，但只能有一个消费这些值的 **接收**（*receiving*）端。

```rust
use std::sync::mpsc;
let (tx, rx) = mpsc::channel();
```

##### 在线程中发送消息

通道的发送端有一个 `send` 方法用来获取需要放入通道的值。`send` 方法返回一个 `Result<T, E>` 类型，所以如果接收端已经被丢弃了，将没有发送值的目标，所以发送操作会返回错误。

```rust
thread::spawn(move || {
  let val = String::from("hi");
  tx.send(val).unwrap();
});
```

##### 在主线程中接受信息

通道的接收端有两个有用的方法：`recv` 和 `try_recv`。

`recv` 会阻塞主线程执行直到从通道中接收一个值。一旦发送了一个值，`recv` 会在一个 `Result<T, E>` 中返回它。当通道发送端关闭，`recv` 会返回一个错误表明不会再有新的值到来了。

`try_recv` 不会阻塞，相反它立刻返回一个 `Result<T, E>`：`Ok` 值包含可用的信息，而 `Err` 值代表此时没有任何消息。如果线程在等待消息过程中还有其他工作时使用 `try_recv` 很有用：可以编写一个循环来频繁调用 `try_recv`，在有可用消息时进行处理，其余时候则处理一会其他工作直到再次检查。

```rust
let received = rx.recv().unwrap();
println!("Got: {}", received);
```

##### 通道与所有权转移

```rust
use std::thread;
use std::sync::mpsc;

fn main() {
  let (tx, rx) = mpsc::channel();

  thread::spawn(move || {
    let val = String::from("hi");
    tx.send(val).unwrap();
    println!("val is {}", val);
  });

  let received = rx.recv().unwrap();
  println!("Got: {}", received);
}
```

一旦将值发送到另一个线程后，那个线程可能会在我们再次使用它之前就将其修改或者丢弃。其他线程对值可能的修改会由于不一致或不存在的数据而导致错误或意外的结果。

##### 发送多个值

```rust
let (tx, rx) = mpsc::channel();

thread::spawn(move || {
    let vals = vec![String::from("more"), String::from("messages"), ];

    for val in vals {
        tx.send(val).unwrap();
        thread::sleep(Duration::from_secs(1));
    }
});

for received in rx {
    println!("Got: {}", received);
}
```

在主线程中，不再显式调用 `recv` 函数：而是将 `rx` 当作一个迭代器。对于每一个接收到的值，我们将其打印出来。当通道被关闭时，迭代器也将结束。

##### 克隆发送者

```rust
let (tx, rx) = mpsc::channel();

let tx1 = tx.clone();
thread::spawn(move || {
    let vals = vec![String::from("more"), String::from("messages"), ];

    for val in vals {
        tx1.send(val).unwrap();
        thread::sleep(Duration::from_secs(1));
    }
});

thread::spawn(move || {
    let vals = vec![String::from("more"), String::from("messages"), ];

    for val in vals {
        tx.send(val).unwrap();
        thread::sleep(Duration::from_secs(1));
    }
});

for received in rx {
    println!("Got: {}", received);
}
```

对通道的发送端调用了 `clone` 方法。这会给一个可以传递给第一个新建线程的发送端句柄。会将原始的通道发送端传递给第二个新建线程。

### 共享状态

多个线程可以同时访问相同的内存位置。

##### 互斥器

互斥器（*mutex*）是 *mutual exclusion* 的缩写，也就是说，任意时刻，其只允许一个线程访问某些数据。为了访问互斥器中的数据，线程首先需要通过获取互斥器的 **锁**（*lock*）来表明其希望访问数据。锁是一个作为互斥器一部分的数据结构，它记录谁有数据的排他访问权。因此，我们描述互斥器为通过锁系统 **保护**（*guarding*）其数据。

要点：

1. 在使用数据之前尝试获取锁。
2. 处理完被互斥器所保护的数据之后，必须解锁数据，这样其他线程才能够获取锁。

##### Mutex\<T>

`Mutex<T>` 是一个智能指针。更准确的说，`lock` 调用 **返回** 一个叫做 `MutexGuard` 的智能指针。这个智能指针实现了 `Deref` 来指向其内部数据；其也提供了一个 `Drop` 实现当 `MutexGuard` 离开作用域时自动释放锁。可以通过 `Mutex` 创建一个共享状态，使用 `lock` 方法获取锁，以访问互斥器中的数据。这个调用会阻塞当前线程，直到拥有锁为止。

```rust
let m = Mutex::new(5);

{
    let mut num = m.lock().unwrap();
    *num = 6;
}

println!("m = {:?}", m);
```

如果另一个线程拥有锁，并且那个线程 panic 了，则 `lock` 调用会失败。在这种情况下，没人能够再获取锁，所以这里选择 `unwrap` 并在遇到这种情况时使线程 panic。一旦获取了锁，就可以将返回值视为一个其内部数据的可变引用了。

##### Arc\<T>

`Arc<T>` 正是一个类似 `Rc<T>` 并可以安全的用于并发环境的类型。字母 “a” 代表 **原子性**（*atomic*），所以这是一个**原子引用计数**（*atomically reference counted*）类型。原子性类型工作起来类似原始类型，不过可以安全的在线程间共享。

```rust
let counter = Arc::new(Mutex::new(0));
let mut handles = vec![];

for _ in 0..10 {
    let counter = Arc::clone(&counter);
    let handle = thread::spawn(move || {
        let mut num = counter.lock().unwrap();
        *num += 1;
    });
    handles.push(handle);
}

for handle in handles {
    handle.join().unwrap();
}

println!("Result: {}", *counter.lock().unwrap());
```

### 通过 Send 允许在线程间转移所有权

`Send` 标记 trait 表明类型的所有权可以在线程间传递。几乎所有的 Rust 类型都是`Send` 的，不过有一些例外，包括 `Rc<T>`：这是不能 `Send` 的，因为如果克隆了 `Rc<T>` 的值并尝试将克隆的所有权转移到另一个线程，这两个线程都可能同时更新引用计数。为此，`Rc<T>` 被实现为用于单线程场景，这时不需要为拥有线程安全的引用计数而付出性能代价。因此，Rust 类型系统和 trait bound 确保永远也不会意外的将不安全的 `Rc<T>` 在线程间发送。

### Sync 允许多线程访问

`ync` 标记 trait 表明一个实现了 `Sync` 的类型可以安全的在多个线程中拥有其值的引用。换一种方式来说，对于任意类型 `T`，如果 `&T`（`T` 的引用）是 `Send` 的话 `T` 就是 `Sync` 的，这意味着其引用就可以安全的发送到另一个线程。类似于 `Send` 的情况，基本类型是 `Sync` 的，完全由 `Sync` 的类型组成的类型也是 `Sync` 的。智能指针 `Rc<T>` 也不是 `Sync` 的，出于其不是 `Send` 相同的原因。`RefCell<T>`和 `Cell<T>` 系列类型不是 `Sync`的。`RefCell<T>` 在运行时所进行的借用检查也不是线程安全的。

