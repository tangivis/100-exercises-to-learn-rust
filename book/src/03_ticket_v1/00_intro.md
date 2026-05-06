# 工单建模 (Modelling a Ticket)

第一章应该已经让你对 Rust 的一些原始类型 (primitive type)、运算符 (operator) 和基本控制流结构有了不错的掌握。\
本章我们要更进一步，讲解 Rust 真正与众不同的特性：**所有权 (ownership)**。\
所有权 (ownership) 让 Rust 既能做到内存安全 (memory-safe)，又能保持高性能，且无需垃圾回收器 (garbage collector)。

作为贯穿全章的示例，我们会使用一个（类似 JIRA 的）工单 (ticket)——你在软件项目中用来跟踪 bug、特性或任务的那种东西。\
我们会尝试用 Rust 来对它建模。这是第一次迭代——到本章结束时，模型既不会完美，也不会非常符合习惯用法 (idiomatic)。但已经足够有挑战性了！\
为了向前推进，你需要掌握若干新的 Rust 概念，例如：

- `struct`，Rust 中定义自定义类型的方式之一
- 所有权 (ownership)、引用 (reference) 和借用 (borrowing)
- 内存管理：栈 (stack)、堆 (heap)、指针 (pointer)、数据布局 (data layout)、析构函数 (destructor)
- 模块 (module) 和可见性 (visibility)
- 字符串 (string)

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/03_ticket_v1/00_intro.md)
