# `Future` 特质 (The `Future` trait)

## 局部 `Rc` 问题 (The local `Rc` problem)

回到 `tokio::spawn` 的签名：

```rust
pub fn spawn<F>(future: F) -> JoinHandle<F::Output>
    where
        F: Future + Send + 'static,
        F::Output: Send + 'static,
{ /* */ }
```

`F` 是 `Send` 究竟意味着什么？\
正如上一节所见，它意味着 `F` 从 spawn 环境捕获的所有值都得是 `Send`。但还不止如此。

任何 _跨越 .await 点_ 持有的值都必须是 `Send`。\
看一个例子：

```rust
use std::rc::Rc;
use tokio::task::yield_now;

fn spawner() {
    tokio::spawn(example());
}

async fn example() {
    // 一个非 `Send` 的值，
    // 在 async 函数 _内部_ 创建
    let non_send = Rc::new(1);
    
    // 一个什么也不做的 `.await` 点
    yield_now().await;

    // `.await` 之后仍需要这个本地非 `Send` 值
    println!("{}", non_send);
}
```

编译器会拒绝这段代码：

```text
error: future cannot be sent between threads safely
    |
5   |     tokio::spawn(example());
    |                  ^^^^^^^^^ 
    | future returned by `example` is not `Send`
    |
note: future is not `Send` as this value is used across an await
    |
11  |     let non_send = Rc::new(1);
    |         -------- has type `Rc<i32>` which is not `Send`
12  |     // A `.await` point
13  |     yield_now().await;
    |                 ^^^^^ 
    |   await occurs here, with `non_send` maybe used later
note: required by a bound in `tokio::spawn`
    |
164 |     pub fn spawn<F>(future: F) -> JoinHandle<F::Output>
    |            ----- required by a bound in this function
165 |     where
166 |         F: Future + Send + 'static,
    |                     ^^^^ required by this bound in `spawn`
```

要理解这是为什么，我们得细化对 Rust 异步模型的理解。

## `Future` 特质 (The `Future` trait)

我们早先说过 `async` 函数返回**未来体 (futures)**——实现了 `Future` 特质的类型。可以把 future 看作一个**状态机 (state machine)**。
它处于两种状态之一：

- **pending（待定）**：计算尚未完成。
- **ready（就绪）**：计算已完成，输出在此。

这点编码在特质定义里：

```rust
trait Future {
    type Output;
    
    // 暂时忽略 `Pin` 和 `Context`
    fn poll(
      self: Pin<&mut Self>, 
      cx: &mut Context<'_>
    ) -> Poll<Self::Output>;
}
```

### `poll`

`poll` 方法是 `Future` 特质的核心。\
future 自身什么也不做。它需要被**轮询 (polled)** 才能推进。\
当你调用 `poll` 时，你在请求 future 做一些工作。
`poll` 尝试推进，然后返回下列之一：

- `Poll::Pending`：future 尚未就绪。你需要稍后再调用 `poll`。
- `Poll::Ready(value)`：future 已完成。`value` 是计算结果，类型为 `Self::Output`。

一旦 `Future::poll` 返回 `Poll::Ready`，就不应再被 poll：future 已经完成，没有事可做了。

### 运行时的角色 (The role of the runtime)

你很少（甚至从不）需要直接调用 `poll`。\
那是异步运行时的工作：它拥有所需的所有信息（`poll` 签名中的 `Context`），确保你的 future 在能推进时尽量推进。

## `async fn` 与 future (`async fn` and futures)

我们已经使用过高层接口——异步函数。\
现在又看了底层原语 `Future` 特质。

它们怎么关联？

每次你把函数标记为异步，那个函数就会返回一个 future。
编译器会把异步函数体转换为一个**状态机 (state machine)**：每个 `.await` 点对应一个状态。

回到我们的 `Rc` 例子：

```rust
use std::rc::Rc;
use tokio::task::yield_now;

async fn example() {
    let non_send = Rc::new(1);
    yield_now().await;
    println!("{}", non_send);
}
```

编译器会把它转换成一个看起来类似下面的枚举：

```rust
pub enum ExampleFuture {
    NotStarted,
    YieldNow(Rc<i32>),
    Terminated,
}
```

调用 `example` 时，它返回 `ExampleFuture::NotStarted`。future 还没被 poll 过，所以什么也没发生。\
当运行时第一次 poll 它时，`ExampleFuture` 会推进到下一个 `.await` 点：会停在状态机的 `ExampleFuture::YieldNow(Rc<i32>)` 阶段，返回 `Poll::Pending`。\
再次被 poll 时，它会执行剩余代码（`println!`）并返回 `Poll::Ready(())`。

看到它的状态机表示 `ExampleFuture` 后，就清楚为什么 `example` 不是 `Send` 了：它持有一个 `Rc`，因此不能 `Send`。

## 让出点 (Yield points)

如刚才用 `example` 看到的，每个 `.await` 点都在 future 生命周期里创建一个新的中间状态。\
这就是 `.await` 点也叫**让出点 (yield points)** 的原因：你的 future 把控制权 _让出 (yield)_ 给正在 poll 它的运行时，允许运行时暂停它，并（必要时）调度另一个任务执行，从而在多个方向上并发推进。

我们会在后面一节再回到让出 (yield) 的重要性。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/08_futures/04_future.md)
