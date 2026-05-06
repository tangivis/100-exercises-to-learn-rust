# 总结 (Wrapping up)

谈到领域建模 (domain modelling)，魔鬼藏在细节里。\
Rust 提供了广泛的工具，帮助你把领域中的约束直接表达在类型系统中，但要做对、写出符合习惯用法 (idiomatic) 的代码，还需要一些练习。

让我们用对 `Ticket` 模型的最后一次打磨来收尾本章。\
我们会为 `Ticket` 的每个字段引入一个新类型，把对应约束封装起来。\
每次有人访问 `Ticket` 字段时，得到的值都保证是有效的——例如得到的是 `TicketTitle` 而不是 `String`。代码其他地方就不必担心标题为空了：只要他们手上有 `TicketTitle`，他们就知道它**按构造 (by construction)** 就是有效的。

这只是利用 Rust 类型系统让代码更安全、更具表达力的一个例子。

## 进一步阅读

- [Parse, don't validate](https://lexi-lambda.github.io/blog/2019/11/05/parse-don-t-validate/)
- [Using types to guarantee domain invariants](https://www.lpalmieri.com/posts/2020-12-11-zero-to-production-6-domain-modelling/)

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/15_outro.md)
