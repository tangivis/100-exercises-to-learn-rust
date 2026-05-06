# 枚举 (Enumerations)

根据你[在前一章](../03_ticket_v1/02_validation.md)写的验证逻辑，工单 (ticket) 实际上只有几个有效状态：`To-Do`、`InProgress` 和 `Done`。\
但当我们看 `Ticket` 结构体的 `status` 字段、或者 `new` 方法里 `status` 参数的类型时，这一点并不显而易见：

```rust
#[derive(Debug, PartialEq)]
pub struct Ticket {
    title: String,
    description: String,
    status: String,
}

impl Ticket {
    pub fn new(
        title: String, 
        description: String, 
        status: String
    ) -> Self {
        // [...]
    }
}
```

这两处我们都用 `String` 来表示 `status` 字段。
`String` 是个非常宽泛的类型——它没法立刻传达出"`status` 字段只能取有限几个值"这条信息。更糟的是，`Ticket::new` 的调用方只能在**运行时**才会发现自己提供的状态是否有效。

我们可以用**枚举 (enumerations)** 做得更好。

## `enum`

枚举是一种可以取一组固定值的类型，每个值称为一个**变体 (variant)**。\
在 Rust 中，使用 `enum` 关键字定义枚举：

```rust
enum Status {
    ToDo,
    InProgress,
    Done,
}
```

`enum` 与 `struct` 一样，定义了**一个新的 Rust 类型**。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/01_enum.md)
