# 异步感知原语 (Async-aware primitives)

如果你浏览 `tokio` 的文档，会注意到它提供了大量"对应"标准库的类型——但带有异步特性：锁、通道、定时器等。

在异步上下文中工作时，应该优先使用这些异步替代品，而不是其同步对应物。

为了理解为什么，我们看 `Mutex`，前一章探索过的互斥锁。

## 案例分析：`Mutex` (Case study: `Mutex`)

看一个简单例子：

```rust
use std::sync::{Arc, Mutex};

async fn run(m: Arc<Mutex<Vec<u64>>>) {
    let guard = m.lock().unwrap();
    http_call(&guard).await;
    println!("Sent {:?} to the server", &guard);
    // `guard` 在这里被 drop
}

/// 把 `v` 用作 HTTP 调用的请求体。
async fn http_call(v: &[u64]) {
  // [...]
}
```

### `std::sync::MutexGuard` 与让出点 (`std::sync::MutexGuard` and yield points)

这段代码能编译，但很危险。

我们尝试在异步上下文中获取一个来自 `std` 的 `Mutex` 的锁。
然后我们跨越一个让出点持有得到的 `MutexGuard`（对 `http_call` 的 `.await`）。

设想有两个任务在单线程运行时上并发地执行 `run`。我们观察到下面的调度事件序列：

```text
     Task A          Task B
        | 
  获取锁
让出给运行时
        | 
        +--------------+
                       |
             尝试获取锁
```

我们死锁了。Task B 永远拿不到锁，因为锁当前由 Task A 持有，而 Task A 在释放锁之前已经让出给运行时，并且因为运行时不能抢占 Task B 而无法被再次调度。

### `tokio::sync::Mutex`

可以通过切换到 `tokio::sync::Mutex` 来解决：

```rust
use std::sync::Arc;
use tokio::sync::Mutex;

async fn run(m: Arc<Mutex<Vec<u64>>>) {
    let guard = m.lock().await;
    http_call(&guard).await;
    println!("Sent {:?} to the server", &guard);
    // `guard` 在这里被 drop
}
```

获取锁现在是异步操作，无法推进时会让出给运行时。\
回到前面的场景，会变成这样：

```text
       Task A          Task B
          | 
     获取锁
   开始 `http_call`
   让出给运行时
          | 
          +--------------+
                         |
                 尝试获取锁
                  无法获取
                  让出给运行时
                         |
          +--------------+
          |
   `http_call` 完成
       释放锁
    让出给运行时
          |
          +--------------+
                         |
                     获取锁
                      [...]
```

一切正常！

### 多线程救不了你 (Multithreaded won't save you)

我们前面的例子用单线程运行时作为执行上下文，但即使使用多线程运行时，同样的风险依然存在。\
唯一区别是制造死锁所需的并发任务数量：
单线程运行时里 2 个就够；多线程运行时里需要 `N+1` 个，其中 `N` 是运行时线程数。

### 缺点 (Downsides)

异步感知的 `Mutex` 带有性能代价。\
如果你确信锁没有显著竞争，_并且_ 你小心从不跨越让出点持有它，仍然可以在异步上下文中使用 `std::sync::Mutex`。

但要权衡这点性能收益与你将承担的活性 (liveness) 风险。

## 其他原语 (Other primitives)

我们用 `Mutex` 作例子，但同样适用于 `RwLock`、信号量等。\
在异步上下文中工作时，优先使用异步感知版本以最小化问题风险。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/08_futures/06_async_aware_primitives.md)
