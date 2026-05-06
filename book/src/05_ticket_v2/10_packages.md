# 库与二进制 (Libraries and binaries)

为 `TicketNewError` 实现 `Error` 特质用的代码不算少吧？\
要手写 `Display` 实现，加上一个 `Error` impl 块。

我们可以用 [`thiserror`](https://docs.rs/thiserror/latest/thiserror/) 减少这些样板代码。它是一个 Rust crate，提供**过程宏 (procedural macro)** 来简化自定义错误类型的创建。\
不过我们走得有点超前：`thiserror` 是第三方 crate，将是我们的第一个依赖！

在深入依赖之前，让我们退一步谈谈 Rust 的包系统 (packaging system)。

## 什么是包？(What is a package?)

Rust 的包由 `Cargo.toml` 文件中的 `[package]` 段定义，`Cargo.toml` 也称为它的**清单 (manifest)**。
在 `[package]` 里你可以设置包的元数据，例如名称和版本。

去看看本节练习目录里的 `Cargo.toml` 文件！

## 什么是 crate？(What is a crate?)

在一个包内，你可以有一个或多个 **crate**，也叫**目标 (target)**。\
最常见的两种 crate 类型是**二进制 crate (binary crate)** 和**库 crate (library crate)**。

### 二进制 (Binaries)

二进制是一个可以编译成**可执行文件 (executable file)** 的程序。\
它必须包含一个名为 `main` 的函数——程序的入口点 (entry point)。`main` 在程序被执行时被调用。

### 库 (Libraries)

而库本身不可执行。你不能 _运行_ 一个库，但可以从依赖它的包里 _导入它的代码_。\
库把代码（即函数、类型等）聚在一起，可被其他包作为**依赖 (dependency)** 使用。

到目前为止你解过的所有练习都是按库的形式组织的，并附带一套测试用例。

### 约定 (Conventions)

围绕 Rust 包有一些约定要记住：

- 包的源代码通常放在 `src` 目录下。
- 如果有 `src/lib.rs` 文件，`cargo` 会推断这个包包含一个库 crate。
- 如果有 `src/main.rs` 文件，`cargo` 会推断这个包包含一个二进制 crate。

你可以在 `Cargo.toml` 中显式声明 target 来覆盖这些默认值——更多细节参见 [`cargo` 的文档](https://doc.rust-lang.org/cargo/reference/cargo-targets.html#cargo-targets)。

记住：一个包可以包含多个 crate，但只能包含一个库 crate。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/10_packages.md)
