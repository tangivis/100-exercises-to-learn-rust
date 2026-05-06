# 读者与写者 (Readers and writers)

我们新的 `TicketStore` 能用，但读取性能不算好：同一时刻只有一个客户端能读取某张特定工单，因为 `Mutex<T>` 不区分读者和写者。

我们可以通过改用另一种锁原语 `RwLock<T>` 来解决这点。\
`RwLock<T>` 代表**读写锁 (read-write lock)**。它允许**多个读者**同时访问数据，但同时只允许一个写者。

`RwLock<T>` 有两个获取锁的方法：`read` 和 `write`。\
`read` 返回一个允许读数据的 guard；`write` 返回一个允许修改数据的 guard。

```rust
use std::sync::RwLock;

// 一个被读写锁保护的整数
let lock = RwLock::new(0);

// 在 RwLock 上获取一个读锁
let guard1 = lock.read().unwrap();

// 在第一个仍活跃时
// 再获取**第二**个读锁
let guard2 = lock.read().unwrap();
```

## 取舍 (Trade-offs)

表面上看 `RwLock<T>` 像不假思索的选择：它提供了 `Mutex<T>` 功能的超集。
为什么还要用 `Mutex<T>` 呢？

有两个关键原因：

- 锁住 `RwLock<T>` 比锁住 `Mutex<T>` 更昂贵。\
  这是因为 `RwLock<T>` 必须跟踪活跃的读者和写者数量，而 `Mutex<T>` 只需跟踪锁是否被持有。
  如果读多于写，这点性能开销不是问题；但如果是写密集 (write-heavy) 工作负载，`Mutex<T>` 可能是更好的选择。
- `RwLock<T>` 可能导致**写者饿死 (writer starvation)**。\
  如果总有读者在等锁，写者可能永远轮不到运行。\
  `RwLock<T>` 不保证读者和写者获取锁的顺序。
  这取决于底层 OS 实现的策略，可能对写者不公平。

我们的场景中，可以预期工作负载是读密集的（多数客户端在读工单而不是修改），所以 `RwLock<T>` 是个好选择。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/12_rw_lock.md)
