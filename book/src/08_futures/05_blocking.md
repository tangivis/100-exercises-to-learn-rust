# 不要阻塞运行时 (Don't block the runtime)

让我们回到让出点 (yield points)。\
与线程不同，**Rust 任务不可被抢占 (preempted)**。

`tokio` 不能自行决定暂停一个任务并替它运行另一个。
控制权**仅在**任务让出时回到执行器——也就是 `Future::poll` 返回 `Poll::Pending` 时，或者对 `async fn` 来说，你 `.await` 一个 future 时。

这给运行时带来一个风险：如果一个任务从不让出，运行时就永远没法运行另一个任务。这叫**阻塞运行时 (blocking the runtime)**。

## 什么算阻塞？(What is blocking?)

多长才算太长？任务不让出能撑多久才会成问题？

这取决于运行时、应用、在飞 (in-flight) 任务数以及许多其他因素。但作为一般经验法则，让出点之间的执行尽量不超过 100 微秒。

## 后果 (Consequences)

阻塞运行时可能导致：

- **死锁 (Deadlocks)**：如果不让出的任务在等待另一个任务完成，而那个任务又在等第一个任务让出，那就是死锁。
  无法推进——除非运行时能把另一个任务调度到不同线程上。
- **饿死 (Starvation)**：其他任务可能跑不起来，或大幅延迟才跑起来，导致性能差（例如尾部延迟高）。

## 阻塞并不总是显而易见 (Blocking is not always obvious)

某些操作通常应当避免在异步代码里执行，例如：

- 同步 I/O。你预测不了它要花多久，而且很可能超过 100 微秒。
- 昂贵的 CPU 密集型计算。

后一类不总是显而易见。例如，对几个元素的向量排序不是问题；如果向量有数十亿条目，评价就变了。

## 怎么避免阻塞 (How to avoid blocking)

好，那么如果你 _必须_ 执行一项符合或可能符合阻塞条件的操作，怎么避免阻塞运行时？\
你需要把工作搬到不同的线程上。你不希望使用所谓的运行时线程——也就是 `tokio` 用来跑任务的那些线程。

`tokio` 为此提供了一个专门的线程池，叫**阻塞池 (blocking pool)**。
你可以用 `tokio::task::spawn_blocking` 把同步操作 spawn 到阻塞池上。`spawn_blocking` 返回一个 future，操作完成时该 future 解析为操作结果。

```rust
use tokio::task;

fn expensive_computation() -> u64 {
    // [...]
}

async fn run() {
    let handle = task::spawn_blocking(expensive_computation);
    // 在此期间做别的事
    let result = handle.await.unwrap();
}
```

阻塞池是长生命周期的。`spawn_blocking` 应当比直接通过 `std::thread::spawn` 创建新线程更快，
因为线程初始化的成本被多次调用摊销了。

## 进一步阅读

- 看看 [Alice Ryhl 关于这个话题的博文](https://ryhl.io/blog/async-what-is-blocking/)。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/08_futures/05_blocking.md)
