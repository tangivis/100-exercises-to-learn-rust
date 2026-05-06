# 运行时架构 (Runtime architecture)

到目前为止我们一直把异步运行时当作一个抽象概念在谈。
我们稍微深入它们的实现方式——你很快会看到，这对我们的代码有影响。

## 风味 (Flavors)

`tokio` 提供两种不同的运行时 _风味 (flavors)_。

可以通过 `tokio::runtime::Builder` 配置运行时：

- `Builder::new_multi_thread` 给你一个**多线程 `tokio` 运行时**
- `Builder::new_current_thread` 则依赖**当前线程 (current thread)** 来执行。

`#[tokio::main]` 默认返回多线程运行时，
而 `#[tokio::test]` 默认使用当前线程运行时。

### 当前线程运行时 (Current thread runtime)

当前线程运行时，顾名思义，仅依赖它启动所在的那个 OS 线程来调度和执行任务。\
使用当前线程运行时时，你有**并发 (concurrency)** 但没有**并行 (parallelism)**：
异步任务会被交错执行，但任意时刻最多只有一个任务在跑。

### 多线程运行时 (Multithreaded runtime)

而使用多线程运行时时，任意时刻最多可以有 `N` 个任务 _并行 (in parallel)_ 运行，其中 `N` 是运行时使用的线程数。默认情况下，`N` 等于可用 CPU 核心数。

还不止于此：`tokio` 实施**工作窃取 (work-stealing)**。\
如果某线程空闲，它不会等着：会尝试找一个新的就绪任务来执行——要么从全局队列拿，要么从另一线程的本地队列偷。\
当应用面对的工作负载在线程间不完全均衡时，工作窃取能带来显著的性能收益，尤其是对尾部延迟。

## 影响 (Implications)

`tokio::spawn` 是风味无关的：不管你跑在多线程还是当前线程运行时上都能工作。代价是它的签名按最坏情况（即多线程）做约束：

```rust
pub fn spawn<F>(future: F) -> JoinHandle<F::Output>
where
    F: Future + Send + 'static,
    F::Output: Send + 'static,
{ /* */ }
```

我们暂时忽略 `Future` 特质，关注其余部分。\
`spawn` 要求所有输入都是 `Send` 且具有 `'static` 生命周期。

`'static` 约束遵循与 `std::thread::spawn` 上 `'static` 约束相同的逻辑：
被 spawn 的任务可能比 spawn 它的上下文更长寿，因此不应依赖任何在 spawn 上下文销毁后可能被回收的本地数据。

```rust
fn spawner() {
    let v = vec![1, 2, 3];
    // 这不会工作，因为 `&v`
    // 活得不够长。
    tokio::spawn(async { 
        for x in &v {
            println!("{x}")
        }
    })
}
```

`Send` 则是 `tokio` 工作窃取策略的直接后果：
在线程 `A` 上 spawn 的任务可能被搬到空闲的线程 `B`，因此需要 `Send` 约束，因为我们要跨线程边界。

```rust
fn spawner(input: Rc<u64>) {
    // 这也不会工作，因为
    // `Rc` 不是 `Send`。
    tokio::spawn(async move {
        println!("{}", input);
    })
}
```

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/08_futures/03_runtime.md)
