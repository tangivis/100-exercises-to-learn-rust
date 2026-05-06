# 取消 (Cancellation)

一个待定 (pending) 的 future 被丢弃时会发生什么？\
运行时不会再 poll 它，因此它不会再有任何进展。
换句话说，它的执行已被**取消 (cancelled)**。

实际中，这经常发生在使用超时时。
例如：

```rust
use tokio::time::timeout;
use tokio::sync::oneshot;
use std::time::Duration;

async fn http_call() {
    // [...]
}

async fn run() {
    // 把 future 用一个 10 毫秒后到期的 `Timeout` 包起来。
    let duration = Duration::from_millis(10);
    if let Err(_) = timeout(duration, http_call()).await {
        println!("Didn't receive a value within 10 ms");
    }
}
```

超时到期时，`http_call` 返回的 future 会被取消。
设想 `http_call` 的函数体是这样：

```rust
use std::net::TcpStream;

async fn http_call() {
    let (stream, _) = TcpStream::connect(/* */).await.unwrap();
    let request: Vec<u8> = /* */;
    stream.write_all(&request).await.unwrap();
}
```

每个让出点都成为一个**取消点 (cancellation point)**。\
`http_call` 不能被运行时抢占，所以只能在它通过 `.await` 让出控制权之后才被丢弃。
这是递归的——例如 `stream.write_all(&request)` 的实现里很可能也有多个让出点。完全可能看到 `http_call` 推送了 _部分_ 请求之后被取消，从而丢弃连接、永远没把请求体发送完。

## 清理 (Clean up)

Rust 的取消机制相当强大——它允许调用方取消正在进行的任务，而无需任务本身做任何配合。\
同时这也可能相当危险。可能希望执行**优雅取消 (graceful cancellation)**，确保在中止操作之前完成一些清理任务。

例如，看下面这个虚构的 SQL 事务 API：

```rust
async fn transfer_money(
    connection: SqlConnection,
    payer_id: u64,
    payee_id: u64,
    amount: u64
) -> Result<(), anyhow::Error> {
    let transaction = connection.begin_transaction().await?;
    update_balance(payer_id, amount, &transaction).await?;
    decrease_balance(payee_id, amount, &transaction).await?;
    transaction.commit().await?;
}
```

被取消时，理想情况是显式中止还在 pending 的事务，而不是把它挂在那。
不幸的是，Rust 没有为这种**异步**清理操作提供万无一失的机制。

最常见的策略是依赖 `Drop` 特质来调度所需的清理工作。可以是：

- 在运行时上 spawn 一个新任务
- 把消息入队到一个通道
- spawn 一个后台线程

最优选择取决于上下文。

## 取消已 spawn 的任务 (Cancelling spawned tasks)

当你用 `tokio::spawn` spawn 一个任务后，你不能再 drop 它；它属于运行时。\
不过，如果需要，你可以用它的 `JoinHandle` 来取消它：

```rust
async fn run() {
    let handle = tokio::spawn(/* some async task */);
    // 取消已 spawn 的任务
    handle.abort();
}
```

## 进一步阅读

- 用 `tokio` 的 `select!` 宏"竞速"两个不同的 future 时要格外小心。
  在循环中重试同一个任务很危险，除非你能确保**取消安全 (cancellation safety)**。
  详情见 [`select!` 的文档](https://tokio.rs/tokio/tutorial/select)。\
  如果你需要交错处理两个异步数据流（例如套接字和通道），优先使用
  [`StreamExt::merge`](https://docs.rs/tokio-stream/latest/tokio_stream/trait.StreamExt.html#method.merge)。
- 在某些场合，[`CancellationToken`](https://docs.rs/tokio-util/latest/tokio_util/sync/struct.CancellationToken.html)
  可能比 `JoinHandle::abort` 更可取。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/08_futures/07_cancellation.md)
