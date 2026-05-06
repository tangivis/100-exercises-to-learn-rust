# 排序 (Ordering)

通过把 `Vec` 换成 `HashMap`，我们提升了工单管理系统的性能，过程中还简化了代码。\
但并非全是好处。在以 `Vec` 为底层的 store 上迭代时，我们能确定工单按添加顺序返回。\
`HashMap` 不是这样：你可以遍历工单，但顺序是随机的。

我们可以通过把 `HashMap` 换成 `BTreeMap` 来恢复一致顺序。

## `BTreeMap`

`BTreeMap` 保证条目按键排序。\
当你需要按特定顺序遍历条目，或需要做范围查询（例如"给我所有 id 在 10 到 20 之间的工单"）时，这很有用。

跟 `HashMap` 一样，`BTreeMap` 的定义上没有特质约束，
但其方法上有。我们看看 `insert`：

```rust
// `K` 和 `V` 分别表示键和值的类型，
// 跟 `HashMap` 一样。
impl<K, V> BTreeMap<K, V> {
    pub fn insert(&mut self, key: K, value: V) -> Option<V>
    where
        K: Ord,
    {
        // 实现
    }
}
```

`Hash` 不再是必须的。取而代之，键类型必须实现 `Ord` 特质。

## `Ord`

`Ord` 特质用来比较值。\
`PartialEq` 用来比较相等性，`Ord` 用来比较顺序。

它定义在 `std::cmp` 中：

```rust
pub trait Ord: Eq + PartialOrd {
    fn cmp(&self, other: &Self) -> Ordering;
}
```

`cmp` 方法返回 `Ordering` 枚举，其值可能是 `Less`、`Equal` 或 `Greater`。\
`Ord` 要求另两个特质也已实现：`Eq` 与 `PartialOrd`。

## `PartialOrd`

`PartialOrd` 是 `Ord` 的较弱版本，正如 `PartialEq` 是 `Eq` 的较弱版本。
看看它的定义就明白了：

```rust
pub trait PartialOrd: PartialEq {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering>;
}
```

`PartialOrd::partial_cmp` 返回 `Option`——并不能保证两个值之间一定可比较。\
例如 `f32` 不实现 `Ord`，因为 `NaN` 值不可比较；同样的原因 `f32` 也不实现 `Eq`。

## 实现 `Ord` 与 `PartialOrd` (Implementing `Ord` and `PartialOrd`)

`Ord` 与 `PartialOrd` 都可以为你的类型派生：

```rust
// 你也得加上 `Eq` 和 `PartialEq`，
// 因为 `Ord` 要求它们。
#[derive(Eq, PartialEq, Ord, PartialOrd)]
struct TicketId(u64);
```

如果你选择（或必须）手动实现，要小心：

- `Ord` 与 `PartialOrd` 必须与 `Eq` 和 `PartialEq` 一致。
- `Ord` 与 `PartialOrd` 必须彼此一致。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/16_btreemap.md)
