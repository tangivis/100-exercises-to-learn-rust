# 索引 (Indexing)

`TicketStore::get` 接受一个 `TicketId` 并返回 `Option<&Ticket>`。\
我们之前见过怎么用 Rust 的索引语法访问数组和向量的元素：

```rust
let v = vec![0, 1, 2];
assert_eq!(v[0], 0);
```

我们怎么为 `TicketStore` 提供同样的体验？\
你猜对了：我们要实现一个特质——`Index`！

## `Index`

`Index` 特质定义在 Rust 标准库中：

```rust
// 略简化
pub trait Index<Idx>
{
    type Output;

    // 必须实现的方法
    fn index(&self, index: Idx) -> &Self::Output;
}
```

它有：

- 一个泛型参数 `Idx`，用于表示索引类型
- 一个关联类型 `Output`，表示通过索引获取到的值的类型

注意 `index` 方法不返回 `Option`。预设是：当你尝试访问不存在的元素时 `index` 会 panic，跟数组和 vec 的索引一样。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/13_index.md)
