# 引入 (Intro)

Rust 的一大承诺是 _无畏并发 (fearless concurrency)_：让安全的并发程序更易编写。
我们到目前为止还没怎么见到这点——所有工作都是单线程的。
是时候改变这个状况了！

本章我们要把工单存储改成多线程。\
我们会有机会接触到 Rust 大部分核心并发特性，包括：

- 线程，使用 `std::thread` 模块
- 消息传递 (message passing)，使用通道 (channel)
- 共享状态 (shared state)，使用 `Arc`、`Mutex` 与 `RwLock`
- `Send` 与 `Sync`，编码 Rust 并发保证的特质

我们也会讨论多线程系统的若干设计模式及其取舍。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/00_intro.md)
