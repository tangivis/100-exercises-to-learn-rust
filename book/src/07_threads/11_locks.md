# 锁、`Send` 与 `Arc` (Locks, `Send` and `Arc`)

你刚实现的打补丁策略有个主要缺点：它有竞争 (racy)。\
如果两个客户端几乎同时为同一张工单发送补丁，服务端会以任意顺序应用它们。
后入队补丁的那位会覆盖前者所做的更改。

## 版本号 (Version numbers)

我们可以尝试用**版本号 (version number)** 修复这个问题。\
每张工单创建时被赋予一个版本号，初始为 `0`。\
每当客户端发送补丁时，必须连同当前的版本号和期望的更改一起发出。服务端只在版本号与它存储的版本号匹配时才应用补丁。

在前面描述的场景中，服务端会拒绝第二个补丁，因为版本号已被第一个补丁递增，与第二个客户端发送的版本号不匹配。

这种方式在分布式系统里相当常见（例如客户端和服务端不共享内存时），称为**乐观并发控制 (optimistic concurrency control)**。\
其思想是：大多数时候不会发生冲突，因此可以为常见场景做优化。
你目前对 Rust 已经了解得够多，可以作为附加练习自行实现这种策略。

## 加锁 (Locking)

我们也可以通过引入**锁 (lock)** 来修复竞争。\
每当客户端想更新工单时，必须先获取它上面的锁。锁活跃期间，其他客户端不能修改这张工单。

Rust 标准库提供了两种不同的锁原语：`Mutex<T>` 和 `RwLock<T>`。\
先从 `Mutex<T>` 开始。它代表 **mut**ual **ex**clusion（互斥锁），是最简单的锁：
不论读写，一次只允许一个线程访问数据。

`Mutex<T>` 包裹它所保护的数据，因此对数据类型是泛型的。\
你不能直接访问数据：类型系统强制你必须先用 `Mutex::lock` 或 `Mutex::try_lock` 获取锁。前者会阻塞直到锁被获取，后者在无法获取锁时立即返回错误。\
两种方法都返回一个 guard 对象，guard 解引用得到数据，从而允许你修改它。当 guard 被丢弃时锁被释放。

```rust
use std::sync::Mutex;

// 一个被互斥锁保护的整数
let lock = Mutex::new(0);

// 在 mutex 上获取一把锁
let mut guard = lock.lock().unwrap();

// 通过 guard 借助其 `Deref` 实现修改数据
*guard += 1;

// 当 `data` 离开作用域时锁被释放
// 也可以显式 drop guard 主动释放
// 或当 guard 离开作用域时隐式释放
drop(guard)
```

## 锁粒度 (Locking granularity)

我们的 `Mutex` 应该包裹什么？\
最简单的选项是用单个 `Mutex` 包裹整个 `TicketStore`。\
这能工作，但会严重限制系统性能：你无法并行读取工单，因为每次读取都得等锁释放。\
这种做法叫**粗粒度锁 (coarse-grained locking)**。

更好的做法是**细粒度锁 (fine-grained locking)**：每张工单由自己的锁保护。
这样客户端可以继续并行处理工单，只要他们没在尝试访问同一张工单。

```rust
// 新结构，每张工单一把锁
struct TicketStore {
    tickets: BTreeMap<TicketId, Mutex<Ticket>>,
}
```

这种方式更高效，但有个缺点：`TicketStore` 必须**意识到 (aware of)** 系统的多线程性质；到目前为止 `TicketStore` 一直福里地忽略线程的存在。\
不管怎样，我们要走这条路。

## 谁持有锁？(Who holds the lock?)

要让整个方案运转起来，锁必须传给想修改工单的客户端。\
客户端随后可以直接修改工单（仿佛拥有 `&mut Ticket`），完成后释放锁。

这有点棘手。\
我们没法把 `Mutex<Ticket>` 通过通道发送，因为 `Mutex` 不是 `Clone`，并且我们不能从 `TicketStore` 中把它移走 (move out)。我们能不能把 `MutexGuard` 发过去？

用一个小例子检验一下这个想法：

```rust
use std::thread::spawn;
use std::sync::Mutex;
use std::sync::mpsc::sync_channel;

fn main() {
    let lock = Mutex::new(0);
    let (sender, receiver) = sync_channel(1);
    let guard = lock.lock().unwrap();

    spawn(move || {
        receiver.recv().unwrap();
    });

    // 尝试把 guard 通过通道发到另一个线程
    sender.send(guard);
}
```

编译器对这段代码不满意：

```text
error[E0277]: `MutexGuard<'_, i32>` cannot be sent between 
              threads safely
   --> src/main.rs:10:7
    |
10  |   spawn(move || {
    |  _-----_^
    | | |
    | | required by a bound introduced by this call
11  | |     receiver.recv().unwrap();
12  | | });
    | |_^ `MutexGuard<'_, i32>` cannot be sent between threads safely
    |
    = help: the trait `Send` is not implemented for 
            `MutexGuard<'_, i32>`, which is required by 
            `{closure@src/main.rs:10:7: 10:14}: Send`
    = note: required for `Receiver<MutexGuard<'_, i32>>` 
            to implement `Send`
note: required because it's used within this closure
```

`MutexGuard<'_, i32>` 不是 `Send`：这是什么意思？

## `Send`

`Send` 是一个标记特质 (marker trait)，表示类型可以安全地从一个线程转移到另一个线程。\
`Send` 也是自动特质 (auto-trait)，跟 `Sized` 一样；编译器根据类型的定义自动为你的类型实现（或不实现）它。\
你也可以为自己的类型手动实现 `Send`，但需要 `unsafe`，因为你必须保证这个类型确实可以安全地在线程间发送，编译器无法自动验证这点。

### 通道的要求 (Channel requirements)

`Sender<T>`、`SyncSender<T>` 和 `Receiver<T>` 当且仅当 `T` 是 `Send` 时才是 `Send`。\
因为它们用于在线程间发送值，如果值本身不是 `Send`，在线程间发送它就不安全。

### `MutexGuard`

`MutexGuard` 不是 `Send`，因为 `Mutex` 实现锁所依赖的底层操作系统原语在某些平台上要求锁必须由获取它的同一个线程释放。\
如果我们把 `MutexGuard` 发到另一个线程，锁就会被另一个线程释放，这会导致未定义行为。

## 我们的挑战 (Our challenges)

总结一下：

- 我们不能通过通道发送 `MutexGuard`，所以不能在服务端侧加锁、再在客户端侧修改工单。
- 我们可以通过通道发送 `Mutex`，因为只要它保护的数据是 `Send`，它本身就是 `Send`，对 `Ticket` 来说成立。
  与此同时，我们既不能把 `Mutex` 从 `TicketStore` 中移走，也不能克隆它。

怎么解开这个难题？\
我们需要换个角度看问题。
要锁住一个 `Mutex`，我们不需要拥有所有权的值。一个共享引用就够了，因为 `Mutex` 使用内部可变性：

```rust
impl<T> Mutex<T> {
    // `&self`，不是 `self`！
    pub fn lock(&self) -> LockResult<MutexGuard<'_, T>> {
        // 实现细节
    }
}
```

因此把共享引用送到客户端就足够了。\
不过我们没法直接这么做，因为引用得是 `'static` 而事实并非如此。\
某种意义上，我们需要一种"具有所有权的共享引用 (owned shared reference)"。事实证明 Rust 有一个类型正好满足要求：`Arc`。

## `Arc` 来救场 (`Arc` to the rescue)

`Arc` 代表**原子引用计数 (atomic reference counting)**。\
`Arc` 包裹一个值，并跟踪有多少对它的引用存在。
当最后一个引用被丢弃时，该值被释放。\
被 `Arc` 包裹的值是不可变的：你只能拿到对它的共享引用。

```rust
use std::sync::Arc;

let data: Arc<u32> = Arc::new(0);
let data_clone = Arc::clone(&data);

// `Arc<T>` 实现了 `Deref<T>`，所以能借助解引用强制转换
// 把 `&Arc<T>` 转为 `&T`
let data_ref: &u32 = &data;
```

如果你有种似曾相识的感觉，没错：`Arc` 听起来与我们讲内部可变性时介绍的引用计数指针 `Rc` 非常相似。区别在于线程安全：`Rc` 不是 `Send`，而 `Arc` 是。
归根结底是引用计数实现方式的差别：`Rc` 用"普通"整数，而 `Arc` 用**原子 (atomic)** 整数，可以安全地跨线程共享与修改。

## `Arc<Mutex<T>>`

把 `Arc` 与 `Mutex` 配在一起，我们终于得到一种类型，它：

- 可以在线程间发送，因为：
  - 当 `T` 是 `Send` 时 `Arc` 是 `Send`，
  - 当 `T` 是 `Send` 时 `Mutex` 是 `Send`。
  - `T` 是 `Ticket`，是 `Send` 的。
- 可以克隆，因为不论 `T` 是什么，`Arc` 都是 `Clone`。
  克隆 `Arc` 会增加引用计数，数据并未被复制。
- 可以用来修改它包裹的数据，因为 `Arc` 让你拿到对 `Mutex<T>` 的共享引用，进而可以获取锁。

我们已具备实现工单存储锁策略所需的所有零件。

## 进一步阅读

- 本课程不涵盖原子操作的细节，更多信息可在
  [`std` 文档](https://doc.rust-lang.org/std/sync/atomic/index.html) 以及
  ["Rust atomics and locks" 一书](https://marabos.nl/atomics/)中找到。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/11_locks.md)
