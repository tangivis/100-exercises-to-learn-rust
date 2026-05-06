# spawn 任务 (Spawning tasks)

你对前一个练习的解决方案大概是这样：

```rust
pub async fn echo(listener: TcpListener) -> Result<(), anyhow::Error> {
    loop {
        let (mut socket, _) = listener.accept().await?;
        let (mut reader, mut writer) = socket.split();
        tokio::io::copy(&mut reader, &mut writer).await?;
    }
}
```

这不算差！\
如果两次进入的连接之间间隔很长，`echo` 函数会处于空闲状态（因为 `TcpListener::accept` 是异步函数），从而允许执行器在此期间运行其他任务。

但我们怎么真正让多个任务并发运行？\
如果总是 `.await` 让异步函数运行到完成，那同一时刻就永远只有一个任务运行。

这就是 `tokio::spawn` 函数登场的地方。

## `tokio::spawn`

`tokio::spawn` 让你把一个任务交给执行器，**而不等待它完成**。\
每次调用 `tokio::spawn`，你都在告诉 `tokio` 在后台**并发**运行被 spawn 的任务，与 spawn 它的任务并行。

下面看怎么用它来并发处理多个连接：

```rust
use tokio::net::TcpListener;

pub async fn echo(listener: TcpListener) -> Result<(), anyhow::Error> {
    loop {
        let (mut socket, _) = listener.accept().await?;
        // spawn 一个后台任务来处理连接，
        // 让主任务可以立即继续接收新连接
        tokio::spawn(async move {
            let (mut reader, mut writer) = socket.split();
            tokio::io::copy(&mut reader, &mut writer).await?;
        });
    }
}
```

### 异步块 (Asynchronous blocks)

这个例子里我们把一个**异步块 (asynchronous block)** `async move { /* */ }` 传给了 `tokio::spawn`。
异步块是把一段代码标记为异步的快捷方式，无需另定义一个异步函数。

### `JoinHandle`

`tokio::spawn` 返回一个 `JoinHandle`。\
你可以用 `JoinHandle` 来 `.await` 后台任务，跟 spawn 线程时使用 `join` 一样。

```rust
pub async fn run() {
    // spawn 一个后台任务把遥测数据发到远端服务器
    let handle = tokio::spawn(emit_telemetry());
    // 与此同时做一些别的有用的事
    do_work().await;
    // 但在遥测数据成功送达之前不返回给调用方
    handle.await;
}

pub async fn emit_telemetry() {
    // [...]
}

pub async fn do_work() {
    // [...]
}
```

### Panic 边界 (Panic boundary)

如果用 `tokio::spawn` spawn 的任务发生 panic，panic 会被执行器捕获。\
如果你不 `.await` 对应的 `JoinHandle`，panic 不会传播给 spawn 它的方。
即使你 `.await` 了 `JoinHandle`，panic 也不会自动传播。
`.await` 一个 `JoinHandle` 会得到 `Result`，错误类型是 [`JoinError`](https://docs.rs/tokio/latest/tokio/task/struct.JoinError.html)。然后你可以调用 `JoinError::is_panic` 检查任务是否 panic 了，并选择如何处理这次 panic——记日志、忽略、或传播。

```rust
use tokio::task::JoinError;

pub async fn run() {
    let handle = tokio::spawn(work());
    if let Err(e) = handle.await {
        if let Ok(reason) = e.try_into_panic() {
            // 任务 panic 了
            // 我们恢复 panic 的展开，
            // 从而把它传播到当前任务
            panic::resume_unwind(reason);
        }
    }
}

pub async fn work() {
    // [...]
}
```

### `std::thread::spawn` 与 `tokio::spawn` 对比

可以把 `tokio::spawn` 看作 `std::thread::spawn` 的异步孪生兄弟。

注意一个关键区别：使用 `std::thread::spawn` 时，你把控制权交给了 OS 调度器。
你无法控制线程如何被调度。

使用 `tokio::spawn` 时，你把控制权交给了一个完全运行在用户态的异步执行器。
底层 OS 调度器并不参与决定接下来运行哪个任务。
现在我们通过所选用的执行器来掌握这个决定。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/08_futures/02_spawn.md)
