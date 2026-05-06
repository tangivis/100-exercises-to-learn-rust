# `Sync`

收尾本章前，我们再谈 Rust 标准库中另一个关键特质：`Sync`。

`Sync` 是自动特质 (auto trait)，跟 `Send` 一样。\
它会自动为所有可以安全地在线程间**共享 (shared)** 的类型实现。

换句话说：当 `&T` 是 `Send` 时 `T` 就是 `Sync`。

## `T: Sync` 不蕴含 `T: Send` (`T: Sync` doesn't imply `T: Send`)

要注意 `T` 可以是 `Sync` 而不是 `Send`。\
例如：`MutexGuard` 不是 `Send`，但它是 `Sync`。

它不是 `Send`，是因为锁必须由获取它的同一个线程释放，因此我们不希望 `MutexGuard` 在另一个线程上被丢弃。\
但它是 `Sync`，因为把 `&MutexGuard` 给另一个线程并不影响锁在哪儿被释放。

## `T: Send` 不蕴含 `T: Sync` (`T: Send` doesn't imply `T: Sync`)

反过来也成立：`T` 可以是 `Send` 而不是 `Sync`。\
例如：`RefCell<T>`（当 `T` 是 `Send` 时）是 `Send`，但不是 `Sync`。

`RefCell<T>` 做运行时借用检查，但它用来跟踪借用的计数器不是线程安全的。
因此，多个线程同时持有 `&RefCell` 会导致数据竞争 (data race)，可能多个线程同时拿到对同一数据的可变引用。所以 `RefCell` 不是 `Sync`。\
`Send` 则没问题，因为把 `RefCell` 发到另一个线程时不会留下任何指向它内部数据的引用，因此不存在并发可变访问的风险。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/14_sync.md)
