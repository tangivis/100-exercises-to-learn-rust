# `thiserror`

绕了点路，对吧？但是值得！\
回到正题：自定义错误类型与 `thiserror`。

## 自定义错误类型 (Custom error types)

我们已经看过如何为自定义错误类型"手动"实现 `Error` 特质。\
想象一下你要给代码库里的大多数错误类型都做一遍——那是不少样板代码 (boilerplate)，对吧？

我们可以用 [`thiserror`](https://docs.rs/thiserror/latest/thiserror/) 减少一部分样板。它是一个 Rust crate，提供**过程宏 (procedural macro)** 来简化自定义错误类型的创建。

```rust
#[derive(thiserror::Error, Debug)]
enum TicketNewError {
    #[error("{0}")]
    TitleError(String),
    #[error("{0}")]
    DescriptionError(String),
}
```

## 你也可以写自己的宏 (You can write your own macros)

到目前为止我们见过的所有 `derive` 宏都来自 Rust 标准库。\
`thiserror::Error` 是我们见到的第一个**第三方** `derive` 宏。

`derive` 宏是**过程宏 (procedural macro)** 的子集——一种在编译期生成 Rust 代码的方式。
本课程不会深入讲过程宏怎么写，但要知道你可以写自己的宏！\
这是一个适合在更进阶的 Rust 课程中讨论的话题。

## 自定义语法 (Custom syntax)

每个过程宏都可以定义自己的语法，这通常会在 crate 文档中说明。
`thiserror` 这边：

- `#[derive(thiserror::Error)]`：借助 `thiserror` 为自定义错误类型派生 `Error` 特质的语法。
- `#[error("{0}")]`：为自定义错误类型每个变体定义 `Display` 实现的语法。
  `{0}` 在显示错误时会被该变体第 0 号字段（这里是 `String`）替换。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/12_thiserror.md)
