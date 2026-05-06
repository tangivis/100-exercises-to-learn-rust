# 通道 (Channels)

到目前为止我们 spawn 的线程都生命周期相当短：拿到输入、做计算、返回结果、关停。

对我们的工单管理系统，我们想做不一样的事：客户端-服务端 (client-server) 架构。

我们将有**一个长生命周期的服务端线程**，负责管理我们的状态——存储的工单。

我们再有**多个客户端线程**。\
每个客户端能向有状态的线程发送**命令 (commands)** 和**查询 (queries)**，从而改变其状态（例如新增一张工单）或检索信息（例如获取一张工单的状态）。\
客户端线程并发运行。

## 通信 (Communication)

到目前为止我们仅有非常有限的父子通信：

- 被 spawn 的线程从父上下文借用/消费数据
- 被 spawn 的线程在 join 时把数据返还给父线程

这对客户端-服务端设计是不够的。\
客户端需要能在服务端线程被启动 _之后_ 与之收发数据。

我们可以用**通道 (channels)** 解决这个问题。

## 通道 (Channels)

Rust 标准库在 `std::sync::mpsc` 模块中提供了**多生产者单消费者 (multi-producer, single-consumer, mpsc)** 通道。\
通道有两种风味：有界 (bounded) 和无界 (unbounded)。我们暂时用无界版本，稍后再讨论它们的优缺点。

通道创建是这样：

```rust
use std::sync::mpsc::channel;

let (sender, receiver) = channel();
```

你得到一个发送者 (sender) 和一个接收者 (receiver)。\
对发送者调用 `send` 把数据推入通道。\
对接收者调用 `recv` 从通道拉取数据。

### 多个发送者 (Multiple senders)

`Sender` 是可克隆的：我们可以创建多个发送者（例如每个客户端线程一个），它们都会把数据推入同一个通道。

`Receiver` 不可克隆：一个通道只能有一个接收者。

这就是 **mpsc**（multi-producer single-consumer）的含义！

### 消息类型 (Message type)

`Sender` 与 `Receiver` 都对类型参数 `T` 泛型。\
那就是能在我们通道上传输的 _消息_ 的类型。

可以是 `u64`、结构体、枚举等。

### 错误 (Errors)

`send` 和 `recv` 都可能失败。\
如果接收者被丢弃，`send` 返回错误。\
如果所有发送者都被丢弃且通道为空，`recv` 返回错误。

换句话说，通道实际上关闭时 `send` 与 `recv` 会出错。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/05_channels.md)
