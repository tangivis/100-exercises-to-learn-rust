# 收尾 (Outro)

Rust 的异步模型相当强大，但确实带来了额外的复杂度。花时间了解你的工具：深入 `tokio` 的文档，熟悉它的原语，让自己物尽其用。

也请记住，语言层面与 `std` 层面都还在持续工作，以打磨并"补全"Rust 的异步故事。
你日常工作中可能会因为某些缺失部分遇到一些粗糙之处。

下面是几条让异步体验大体顺畅的建议：

- **选定一个运行时并坚持使用它。**\
  某些原语（例如定时器、I/O）在运行时之间不可移植。试图混用运行时很可能给你带来麻烦。试图写出运行时无关的代码可能显著增加代码库复杂度。能避免就避免。
- **目前还没有稳定的 `Stream`/`AsyncIterator` 接口。**\
  `AsyncIterator` 在概念上是异步产生新元素的迭代器。设计工作正在进行，但（暂时）还没有共识。
  如果你用 `tokio`，把 [`tokio_stream`](https://docs.rs/tokio-stream/latest/tokio_stream/) 当作首选接口。
- **小心缓冲。**\
  它经常是隐蔽 bug 的根源。详情见
  ["Barbara battles buffered streams"](https://rust-lang.github.io/wg-async/vision/submitted_stories/status_quo/barbara_battles_buffered_streams.html)。
- **异步任务还没有作用域线程的等价物。**\
  详情见 ["The scoped task trilemma"](https://without.boats/blog/the-scoped-task-trilemma/)。

不要被这些注意事项吓到：异步 Rust 正被有效地用在 _巨型_ 规模上（例如 AWS、Meta），驱动核心服务。\
如果你打算用 Rust 构建网络应用，你将必须掌握它。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/08_futures/08_outro.md)
