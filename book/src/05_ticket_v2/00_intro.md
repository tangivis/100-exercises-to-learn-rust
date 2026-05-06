# 工单建模，第二部分 (Modelling A Ticket, pt. 2)

我们在前几章打磨过的 `Ticket` 结构体是个不错的起点，但它仍然在大喊"我是个 Rust 新手 (Rustacean)！"。

我们用本章来打磨 Rust 领域建模 (domain modelling) 的能力。
途中我们还要引入几个新概念：

- `enum`，Rust 在数据建模上最强大的特性之一
- `Option` 类型，用于建模可空值 (nullable values)
- `Result` 类型，用于建模可恢复错误 (recoverable errors)
- `Debug` 和 `Display` 特质，用于打印
- `Error` 特质，用于标记错误类型
- `TryFrom` 与 `TryInto` 特质，用于可能失败的转换 (fallible conversions)
- Rust 的包系统 (package system)：什么是库 (library)、什么是二进制 (binary)、如何使用第三方 crate

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/00_intro.md)
