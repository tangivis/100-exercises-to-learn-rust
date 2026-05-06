# 异步函数 (Asynchronous functions)

到目前为止你写的所有函数和方法都是急切 (eager) 的。\
你不调用它们就什么也不会发生。但一旦调用，它们会运行直到完成：把**所有**工作做完，然后返回输出。

有时这并不理想。\
例如，如果你写一个 HTTP 服务器，可能会有大量的**等待 (waiting)**：等待请求体到达、等待数据库响应、等待下游服务回复，等等。

要是你能在等待的同时做别的事呢？\
要是你能在某个计算进行到一半时选择放弃呢？\
要是你能选择把另一个任务的优先级置于当前任务之上呢？

这就轮到**异步函数 (asynchronous functions)** 上场了。

## `async fn`

你用 `async` 关键字定义异步函数：

```rust
use tokio::net::TcpListener;

// 这个函数是异步的
async fn bind_random() -> TcpListener {
    // [...]
}
```

如果你像调用普通函数一样调用 `bind_random` 会怎样？

```rust
fn run() {
    // 调用 `bind_random`
    let listener = bind_random();
    // 接下来呢？
}
```

什么也不会发生！\
Rust 在你调用 `bind_random` 时不会开始执行它，
也不会作为后台任务启动它（你可能基于其他语言的经验会这么期待）。
Rust 中的异步函数是**惰性 (lazy)** 的：你不显式要求它们做事，它们就什么也不做。
用 Rust 的术语说，`bind_random` 返回一个**未来体 (future)**，一种表示可能稍后完成的计算的类型。它们叫 future 是因为它们实现了 `Future` 特质——本章稍后会详细讲解的接口。

## `.await`

要让异步函数执行任务，最常见的方式是使用 `.await` 关键字：

```rust
use tokio::net::TcpListener;

async fn bind_random() -> TcpListener {
    // [...]
}

async fn run() {
    // 调用 `bind_random` 并等待它完成
    let listener = bind_random().await;
    // 现在 `listener` 就绪
}
```

`.await` 直到异步函数运行完成（例如上面例子里 `TcpListener` 创建完成）才把控制权交还给调用方。

## 运行时 (Runtimes)

如果你感到困惑，那是合理的！\
我们刚说异步函数的好处是它们不会一次把**所有**工作做完。然后我们又引入了 `.await`，它直到异步函数运行完成才返回。难道不是把我们想解决的问题又引回来了吗？这有什么意义？

并非如此！调用 `.await` 时幕后发生了很多事！\
你正把控制权让给一个**异步运行时 (async runtime)**，也叫**异步执行器 (async executor)**。
执行器是魔法发生的地方：它负责管理你所有正在进行的异步**任务 (tasks)**。具体来说，它在两个目标之间取得平衡：

- **推进 (Progress)**：确保任务在能推进时尽量推进。
- **效率 (Efficiency)**：如果一个任务在等待某事，确保另一个任务能在此期间运行，充分利用可用资源。

### 没有默认运行时 (No default runtime)

Rust 在异步编程的方式上相当独特：没有默认运行时。
标准库不附带运行时。你需要自己带一个！

大多数情况下，你会从生态中现有的选项中选一个。
有些运行时被设计为通用，是大多数应用的稳妥选择。
`tokio` 和 `async-std` 属于这一类。还有些运行时为特定场景做了优化——例如嵌入式系统的 `embassy`。

整门课程我们都依赖 `tokio`，它是 Rust 中通用异步编程最流行的运行时。

### `#[tokio::main]`

你可执行文件的入口点 `main` 函数必须是同步函数。
你应当在它里面设置并启动你选的异步运行时。

大多数运行时提供宏来简化这个工作。`tokio` 的是 `tokio::main`：

```rust
#[tokio::main]
async fn main() {
    // 这里写你的异步代码
}
```

它会展开为：

```rust
fn main() {
    let rt = tokio::runtime::Runtime::new().unwrap();
    rt.block_on(
        // 这里写你的异步函数
        // [...]
    );
}
```

### `#[tokio::test]`

测试也一样：必须是同步函数。\
每个测试函数在其自己的线程中运行，如果你需要在测试中执行异步代码，需要自己设置并启动异步运行时。\
`tokio` 提供了 `#[tokio::test]` 宏来简化这件事：

```rust
#[tokio::test]
async fn my_test() {
    // 这里写你的异步测试代码
}
```

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/08_futures/01_async_fn.md)
