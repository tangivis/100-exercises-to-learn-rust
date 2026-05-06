# 可变索引 (Mutable indexing)

`Index` 只允许只读访问，不允许你修改取出的值。

## `IndexMut`

如果你想允许可变性，需要实现 `IndexMut` 特质。

```rust
// 略简化
pub trait IndexMut<Idx>: Index<Idx>
{
    // 必须实现的方法
    fn index_mut(&mut self, index: Idx) -> &mut Self::Output;
}
```

`IndexMut` 只能在类型已经实现了 `Index` 的前提下实现，因为它解锁的是一项 _额外_ 能力。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/14_index_mut.md)
