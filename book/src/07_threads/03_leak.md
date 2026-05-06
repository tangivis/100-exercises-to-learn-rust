# 泄漏数据 (Leaking data)

把引用传给 spawn 的线程的主要顾虑是 use-after-free bug：
通过指向已经被释放/回收内存区域的指针来访问数据。\
如果你处理的是堆分配的数据，可以告诉 Rust 你永远不会回收那块内存来避开这个问题：你刻意选择**泄漏内存 (leak memory)**。

可以用 Rust 标准库里的 `Box::leak` 方法做到这点：

```rust
// 通过把 `u32` 包到 `Box` 里来在堆上分配它。
let x = Box::new(41u32);
// 用 `Box::leak` 告诉 Rust 你永远不会释放这块堆分配。
// 你因此能拿回一个 'static 引用。
let static_ref: &'static mut u32 = Box::leak(x);
```

## 数据泄漏是进程级的 (Data leakage is process-scoped)

泄漏数据是危险的：如果你不停泄漏内存，最终会用尽内存并以"内存不足 (out-of-memory)" 错误崩溃。

```rust
// 如果你让它跑一阵子，
// 它最终会用光所有可用内存。
fn oom_trigger() {
    loop {
        let v: Vec<usize> = Vec::with_capacity(1024);
        v.leak();
    }
}
```

同时，通过 `leak` 方法泄漏的内存并未真正被遗忘。\
操作系统能把每块内存区域映射到负责它的进程。
进程退出时，操作系统会回收那块内存。

记住这点的话，泄漏内存在以下情形是可以接受的：

- 需要泄漏的内存量是有界 (bounded)/事先已知的，或者
- 你的进程是短生命周期的，且你确信在它退出之前不会耗尽所有可用内存

如果你的用例允许，"让 OS 处理它"是一种完全有效的内存管理策略。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/03_leak.md)
