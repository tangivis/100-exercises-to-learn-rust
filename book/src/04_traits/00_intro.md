# 特质 (Traits)

上一章我们覆盖了 Rust 类型系统和所有权 (ownership) 系统的基础。\
现在该深入挖掘了：我们要探索**特质 (trait)**——Rust 对接口 (interface) 的诠释。

一旦你了解了特质，就会开始在各处看到它们的影子。\
事实上，前一章你已经在不断接触特质了，例如 `.into()` 调用以及 `==`、`+` 这类运算符。

除了把特质作为一个概念讨论之外，本章还会覆盖 Rust 标准库 (standard library) 中定义的几个关键特质：

- 运算符特质（例如 `Add`、`Sub`、`PartialEq` 等）
- `From` 与 `Into`，用于不会失败的转换 (infallible conversions)
- `Clone` 与 `Copy`，用于复制值
- `Deref` 与解引用强制转换 (deref coercion)
- `Sized`，用于标记大小已知的类型
- `Drop`，用于自定义清理逻辑

既然要谈到类型转换，我们也会顺便填补上一章遗留的一些"知识空白"——比如 `"A title"` 究竟是什么？是时候多了解一下切片 (slice) 了！

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/00_intro.md)
