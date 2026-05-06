# 有界 vs 无界通道 (Bounded vs unbounded channels)

到目前为止我们都在使用无界通道。\
你想发多少消息都行，通道会自己增长以容纳它们。\
在多生产者单消费者场景里，这可能有问题：如果生产者入队消息的速率比消费者处理的速率更快，通道会持续增长，可能消耗光所有可用内存。

我们的建议是：在生产系统里**永远不要**使用无界通道。\
你应当总是用**有界通道 (bounded channel)** 对可入队的消息数量设置上限。

## 有界通道 (Bounded channels)

有界通道有固定容量。\
可以通过用大于零的容量调用 `sync_channel` 来创建一个：

```rust
use std::sync::mpsc::sync_channel;

let (sender, receiver) = sync_channel(10);
```

`receiver` 类型同前，依然是 `Receiver<T>`。\
`sender` 这次是 `SyncSender<T>` 实例。

### 发送消息 (Sending messages)

通过 `SyncSender` 发送消息有两种不同的方法：

- `send`：如果通道有空间，入队消息并返回 `Ok(())`。\
  如果通道满了，会阻塞并等待直到有可用空间。
- `try_send`：如果通道有空间，入队消息并返回 `Ok(())`。\
  如果通道满了，会返回 `Err(TrySendError::Full(value))`，其中 `value` 是没能发送出去的消息。

依据你的用例选择其一。

### 背压 (Backpressure)

使用有界通道的主要优点是它们提供了一种**背压 (backpressure)** 形式。\
它们强制生产者在消费者跟不上时减速。
背压随后可以在系统中传播，可能影响整个架构，防止终端用户用海量请求把系统压垮。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/09_bounded.md)
