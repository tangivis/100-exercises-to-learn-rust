# 验证 (Validation)

让我们回到工单 (ticket) 的定义：

```rust
struct Ticket {
    title: String,
    description: String,
    status: String,
}
```

我们对 `Ticket` 结构体的字段使用了"原始"类型。
这意味着用户可以创建一个标题为空、描述超长、或者状态毫无意义（例如 "Funny"）的工单。\
我们能做得更好！

## 进一步阅读

- 完整浏览一下 [`String` 的文档](https://doc.rust-lang.org/std/string/struct.String.html)，
  了解它提供的方法。完成练习时你会用到它！

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/03_ticket_v1/02_validation.md)
