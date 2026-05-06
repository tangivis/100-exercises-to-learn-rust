# 异步 Rust (Async Rust)

线程并不是 Rust 中编写并发程序的唯一方式。\
本章我们要探索另一种方式：**异步编程 (asynchronous programming)**。

具体来说，你将获得对以下内容的入门：

- `async`/`.await` 关键字，让你毫不费力地写异步代码
- `Future` 特质，表示可能尚未完成的计算
- `tokio`，最流行的运行异步代码的运行时
- Rust 异步模型的协作 (cooperative) 性质，以及它如何影响你的代码

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/08_futures/00_intro.md)
