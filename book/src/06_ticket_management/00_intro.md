# 引入 (Intro)

上一章我们在真空中对 `Ticket` 进行建模：定义了字段及其约束，学习了如何在 Rust 中尽量好地表达它们，但没有考虑 `Ticket` 在更大系统中的位置。
本章我们要围绕 `Ticket` 构建一个简单的工作流，引入一个（粗糙的）管理系统来存储和检索工单。

任务会让我们有机会探索新的 Rust 概念，例如：

- 栈分配的数组 (Stack-allocated arrays)
- `Vec`，可增长的数组类型
- `Iterator` 与 `IntoIterator`，用于在集合上迭代
- 切片 (slice) `&[T]`，用于处理集合的一部分
- 生命周期 (lifetime)，用于描述引用有效多久
- `HashMap` 与 `BTreeMap`，两个键值数据结构
- `Eq` 与 `Hash`，用于在 `HashMap` 中比较键
- `Ord` 与 `PartialOrd`，用于操作 `BTreeMap`
- `Index` 与 `IndexMut`，用于访问集合中的元素

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/00_intro.md)
