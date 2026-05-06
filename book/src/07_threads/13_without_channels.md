# 设计回顾 (Design review)

我们花点时间回顾走过的路。

## 无锁 + 通道串行化 (Lockless with channel serialization)

我们多线程工单存储的第一版实现使用了：

- 一个长生命周期线程（服务端），持有共享状态
- 多个客户端，从各自线程通过通道向它发送请求

不需要对状态加锁，因为只有服务端在修改状态。这是因为
"收件箱"通道天然把进入的请求**串行化 (serialized)**：服务端逐一处理。\
我们之前讨论过这种方式在打补丁行为上的局限，但没讨论原始设计的性能影响：服务端一次只能处理一个请求，包括读。

## 细粒度锁 (Fine-grained locking)

随后我们转向了更复杂的设计：每张工单由自己的锁保护，客户端可以独立决定要读取还是原子修改某张工单，并获取相应的锁。

这种设计允许更好的并行性（即多个客户端可同时读取工单），但根本上仍然是**串行 (serial)** 的：服务端逐一处理命令；尤其是它逐一发放锁给客户端。

我们能不能干脆去掉通道，让客户端直接访问 `TicketStore`，纯靠锁来同步访问？

## 移除通道 (Removing channels)

我们要解决两个问题：

- 跨线程共享 `TicketStore`
- 同步对 store 的访问

### 跨线程共享 `TicketStore` (Sharing `TicketStore` across threads)

我们希望所有线程引用同一份状态，否则就不算真正的多线程系统——只是并行运行多个单线程系统。\
我们之前在跨线程共享锁时已经遇到过这个问题：可以用 `Arc`。

### 同步对 store 的访问 (Synchronizing access to the store)

有一种交互一直靠通道串行化提供的无锁性质：往 store 里插入（或移除）工单。\
如果我们移除通道，需要引入（另一把）锁来同步对 `TicketStore` 本身的访问。

如果用 `Mutex`，那就没必要再为每张工单加 `RwLock`：`Mutex` 已经把对整个 store 的访问串行化，无论如何都无法并行读取工单。\
如果改用 `RwLock`，则可以并行读取工单，只需在插入或移除工单时暂停所有读。

让我们沿这条路走下去看看会到哪儿。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/13_without_channels.md)
