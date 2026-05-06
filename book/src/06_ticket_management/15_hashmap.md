# `HashMap`

我们的 `Index`/`IndexMut` 实现并不理想：要按 id 取出工单需要遍历整个 `Vec`；算法复杂度是 `O(n)`，其中 `n` 是 store 中工单的数量。

我们可以通过用不同的数据结构存储工单来做得更好：`HashMap<K, V>`。

```rust
use std::collections::HashMap;

// 类型推断让我们可以省略显式类型签名
//（这个例子里会是 `HashMap<String, String>`）。
let mut book_reviews = HashMap::new();

book_reviews.insert(
    "Adventures of Huckleberry Finn".to_string(),
    "My favorite book.".to_string(),
);
```

`HashMap` 处理键值对 (key-value pair)。它对二者都是泛型的：`K` 是键类型的泛型参数，`V` 是值类型的泛型参数。

插入、查询和删除的预期成本都是**常数级**的，`O(1)`。
听起来正合我们的需求，是不是？

## 键的要求 (Key requirements)

`HashMap` 结构体定义本身没有特质约束，但它的方法上有。我们看看 `insert`：

```rust
// 略简化
impl<K, V> HashMap<K, V>
where
    K: Eq + Hash,
{
    pub fn insert(&mut self, k: K, v: V) -> Option<V> {
        // [...]
    }
}
```

键类型必须实现 `Eq` 和 `Hash` 特质。\
我们一一深入。

## `Hash`

哈希函数（hashing function 或 hasher）把可能无穷的值集合（例如所有可能的字符串）映射到有界范围（例如一个 `u64` 值）。\
有许多不同的哈希函数，各有不同特性（速度、碰撞风险、可逆性等）。

`HashMap`，顾名思义，幕后使用了哈希函数。
它对你的键做哈希，再用这个哈希值来存储/检索关联值。
这种策略要求键类型必须可哈希，因此 `K` 上有 `Hash` 特质约束。

`Hash` 特质位于 `std::hash` 模块下：

```rust
pub trait Hash {
    // 必须实现的方法
    fn hash<H>(&self, state: &mut H)
       where H: Hasher;
}
```

你很少需要手动实现 `Hash`。大多数情况下你会派生它：

```rust
#[derive(Hash)]
struct Person {
    id: u32,
    name: String,
}
```

## `Eq`

`HashMap` 必须能比较键的相等性。这在处理哈希碰撞 (hash collision) 时尤其重要——即两个不同的键被哈希到同一个值。

你可能想：这不就是 `PartialEq` 吗？几乎是！\
对 `HashMap` 来说 `PartialEq` 不够，因为它不保证自反性 (reflexivity)，即 `a == a` 总为 `true`。\
例如，浮点数 (`f32` 和 `f64`) 实现了 `PartialEq`，但不满足自反性：`f32::NAN == f32::NAN` 是 `false`。\
对 `HashMap` 正确工作来说自反性至关重要：没有它，你就无法使用插入时使用的同一个键再从 map 中取回值。

`Eq` 特质在 `PartialEq` 之上加了自反性属性：

```rust
pub trait Eq: PartialEq {
    // 没有附加方法
}
```

它是个标记特质 (marker trait)：不引入新方法，只是让你向编译器声明 `PartialEq` 中实现的相等逻辑是自反的。

派生 `PartialEq` 时可以一并自动派生 `Eq`：

```rust
#[derive(PartialEq, Eq)]
struct Person {
    id: u32,
    name: String,
}
```

## `Eq` 与 `Hash` 是关联的 (`Eq` and `Hash` are linked)

`Eq` 与 `Hash` 之间有一条隐式契约：如果两个键相等，它们的哈希值也必须相等。
这对 `HashMap` 正确工作至关重要。如果你违反这条契约，使用 `HashMap` 时就会得到没意义的结果。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/15_hashmap.md)
