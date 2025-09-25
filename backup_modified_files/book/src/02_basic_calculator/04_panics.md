# 恐慌 (Panics)

让我们回到你为["变量"章节](02_variables.md)编写的 `speed` 函数。
它可能看起来像这样：

```rust
fn speed(start: u32, end: u32, time_elapsed: u32) -> u32 {
    let distance = end - start;
    distance / time_elapsed
}
```

如果你有敏锐的眼光，你可能已经发现了一个问题[^one]：如果 `time_elapsed` 为零会发生什么？

你可以在[Rust playground](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=36e5ddbe3b3f741dfa9f74c956622bac)上尝试一下！\
程序将退出并显示以下错误信息：

```text
thread 'main' panicked at src/main.rs:3:5:
attempt to divide by zero
```

这就是所谓的**恐慌 (panic)**。\
恐慌是 Rust 用来表示出现问题严重到程序无法继续执行的方式，它是一个**不可恢复的错误 (unrecoverable error)**[^catching]。除零操作就被归类为这样的错误。

## panic! 宏 (macro)

你可以通过调用 `panic!` 宏[^macro]来有意触发恐慌：

```rust
fn main() {
    panic!("这是一个恐慌！");
    // 下面这行永远不会被执行
    let x = 1 + 2;
}
```

在 Rust 中有其他机制来处理可恢复的错误，我们[稍后会介绍](../05_ticket_v2/06_fallibility.md)。
目前我们将坚持使用恐慌作为一种残酷但简单的权宜之计。

## 进一步阅读

- [panic! 宏文档](https://doc.rust-lang.org/std/macro.panic.html)

[^one]: `speed` 函数还有另一个问题，我们很快就会解决。你能发现它吗？

[^catching]: 你可以尝试捕获恐慌，但这应该是为非常特定情况保留的最后手段。

[^macro]: 如果后面跟着 `!`，那就是宏调用。现在可以把宏看作是"辣味函数"。我们会在课程后面更详细地介绍它们。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/02_basic_calculator/04_panics.md)
