# `impl Trait`

`TicketStore::to_dos` 返回 `Vec<&Ticket>`。\
这个签名意味着每次调用 `to_dos` 都会引入一次新的堆分配——这有时是不必要的，取决于调用方对结果做什么。
如果 `to_dos` 返回迭代器而非 `Vec` 会更好，让调用方自行决定要不要把结果收集到 `Vec` 里、还是直接遍历。

但这有点棘手！
下面这段实现里 `to_dos` 的返回类型该是什么？

```rust
impl TicketStore {
    pub fn to_dos(&self) -> ??? {
        self.tickets.iter().filter(|t| t.status == Status::ToDo)
    }
}
```

## 不可命名的类型 (Unnameable types)

`filter` 方法返回一个 `std::iter::Filter` 实例，其定义为：

```rust
pub struct Filter<I, P> { /* 字段省略 */ }
```

其中 `I` 是被过滤迭代器的类型，`P` 是用于过滤元素的谓词。\
我们知道 `I` 这里是 `std::slice::Iter<'_, Ticket>`，但 `P` 呢？\
`P` 是个闭包，是**匿名函数 (anonymous function)**。顾名思义，闭包没有名字，所以我们没法在代码里写出来。

Rust 对此有解：**`impl Trait`**。

## `impl Trait`

`impl Trait` 是一项允许你返回一个类型而不必指明其名字的特性。
你只声明该类型实现了哪些特质，剩下的由 Rust 弄清楚。

这里我们想返回一个对 `Ticket` 引用的迭代器：

```rust
impl TicketStore {
    pub fn to_dos(&self) -> impl Iterator<Item = &Ticket> {
        self.tickets.iter().filter(|t| t.status == Status::ToDo)
    }
}
```

就这么简单！

## 是泛型吗？(Generic?)

返回位置上的 `impl Trait` **不是**泛型参数。

泛型是占位符，由函数调用方填入具体类型。
带泛型参数的函数是**多态 (polymorphic)** 的：可以用不同类型调用，编译器会为每种类型生成不同的实现。

`impl Trait` 不是这样的。
带 `impl Trait` 的函数返回类型在编译期是**固定 (fixed)** 的，编译器只会为它生成一份实现。
所以 `impl Trait` 也叫**不透明返回类型 (opaque return type)**：调用方不知道返回值的精确类型，只知道它实现了所指定的特质。但编译器知道精确类型，并不涉及多态。

## RPIT

如果你读 RFC 或关于 Rust 的深入文章，可能会碰到缩写 **RPIT**。\
它代表 **"Return Position Impl Trait"**，指的就是在返回位置使用 `impl Trait`。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/08_impl_trait.md)
