# 更新操作 (Update operations)

到目前为止我们只实现了插入和检索操作。\
看看怎么把系统扩展为提供更新操作。

## 旧版的更新 (Legacy updates)

在系统的非线程版本里，更新相当直接：`TicketStore` 暴露了一个 `get_mut` 方法，允许调用方拿到工单的可变引用并修改它。

## 多线程下的更新 (Multithreaded updates)

同样的策略在当前的多线程版本里行不通。借用检查器会拦下我们：`SyncSender<&mut Ticket>` 不是 `'static`，因为 `&mut Ticket` 不满足 `'static` 生命周期，因此它们不能被传给 `std::thread::spawn` 的闭包捕获。

要绕开这个限制有几种方式。我们会在接下来的几个练习里探索其中几种。

### 打补丁 (Patching)

我们没法把 `&mut Ticket` 通过通道发送，所以无法在客户端侧进行修改。\
那能不能在服务端侧修改？

可以——只要我们告诉服务端要改什么。换言之，给服务端发送一个**补丁 (patch)**：

```rust
struct TicketPatch {
    id: TicketId,
    title: Option<TicketTitle>,
    description: Option<TicketDescription>,
    status: Option<TicketStatus>,
}
```

`id` 字段是必需的，因为它用来识别要更新哪张工单。\
其他字段都是可选的：

- 如果某字段为 `None`，意味着该字段不应被改变。
- 如果某字段为 `Some(value)`，意味着该字段应被改为 `value`。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/10_patch.md)
