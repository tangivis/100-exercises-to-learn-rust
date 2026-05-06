# 调整大小 (Resizing)

我们说 `Vec` 是"可增长"的向量类型，但这究竟意味着什么？
如果你尝试向已经达到最大容量的 `Vec` 中插入元素，会发生什么？

```rust
let mut numbers = Vec::with_capacity(3);
numbers.push(1);
numbers.push(2);
numbers.push(3); // 已达最大容量
numbers.push(4); // 这里会发生什么？
```

`Vec` 会**调整自身大小 (resize)**。\
它会向分配器请求一块新的（更大的）堆内存，把元素复制过去，然后释放旧内存。

这个操作可能很昂贵，因为它涉及一次新的内存分配以及把所有现有元素复制过去。

## `Vec::with_capacity`

如果你大致知道要在 `Vec` 中存多少元素，可以用 `Vec::with_capacity` 方法预先分配足够的内存。\
这能避免 `Vec` 增长时的一次新分配，但如果你高估了实际用量，也会浪费内存。

依个案权衡。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/03_resizing.md)
