# 生命周期 (Lifetimes)

为了在 `for` 循环中获得最大便利，我们尝试通过为 `&TicketStore` 实现 `IntoIterator` 来完成前一个练习。

先填上实现中最"显而易见"的部分：

```rust
impl IntoIterator for &TicketStore {
    type Item = &Ticket;
    type IntoIter = // 这里写什么？

    fn into_iter(self) -> Self::IntoIter {
        self.tickets.iter()
    }
}
```

`type IntoIter` 应当是什么？\
直觉上，应当是 `self.tickets.iter()` 返回的类型，也就是 `Vec::iter()` 返回的类型。\
查看标准库文档可以发现 `Vec::iter()` 返回 `std::slice::Iter`。
`Iter` 的定义是：

```rust
pub struct Iter<'a, T> { /* 字段省略 */ }
```

`'a` 是一个**生命周期参数 (lifetime parameter)**。

## 生命周期参数 (Lifetime parameters)

生命周期 (lifetime) 是 Rust 编译器用来跟踪某个引用（不论可变还是不可变）有效多久的**标签 (label)**。\
引用的生命周期受它所引用的值的作用域 (scope) 约束。Rust 总是在编译期确保引用不会在它所指向的值被丢弃之后再被使用，从而避免悬垂指针 (dangling pointer) 与释放后使用 (use-after-free) bug。

这听起来应该很熟悉：我们在讨论所有权与借用时已经看过这些概念在起作用。
生命周期只是一种**命名 (name)** 某个引用有效多久的方式。

当你有多个引用、需要明确它们之间的**关系**时，命名变得重要。
我们看看 `Vec::iter()` 的签名：

```rust
impl <T> Vec<T> {
    // 略简化
    pub fn iter<'a>(&'a self) -> Iter<'a, T> {
        // [...]
    }
}
```

`Vec::iter()` 是一个对生命周期参数 `'a` 泛型的函数。\
`'a` 用于把 `Vec` 的生命周期与 `iter()` 返回的 `Iter` 的生命周期**绑定 (tie together)** 起来。
用大白话说：`iter()` 返回的 `Iter` 不能比创建它的 `Vec` 引用 (`&self`) 活得更久。

这一点很重要，因为正如我们讨论过的，`Vec::iter` 返回的迭代器是对 `Vec` 元素**引用**的迭代器。
如果 `Vec` 被丢弃，迭代器返回的引用就失效了。Rust 必须确保这不会发生，生命周期就是它用来强制执行这条规则的工具。

## 生命周期省略 (Lifetime elision)

Rust 有一组**生命周期省略规则 (lifetime elision rules)**，允许你在很多场合省略显式的生命周期标注。
例如，`Vec::iter` 在 `std` 源码中的定义是这样的：

```rust
impl <T> Vec<T> {
    pub fn iter(&self) -> Iter<'_, T> {
        // [...]
    }
}
```

`Vec::iter()` 的签名中没有出现显式的生命周期参数。
省略规则意味着 `iter()` 返回的 `Iter` 的生命周期与 `&self` 引用的生命周期绑定。
你可以把 `'_` 看作 `&self` 引用生命周期的**占位符 (placeholder)**。

参见[参考资料 (References)](#参考资料-references) 一节获取生命周期省略的官方文档链接。\
大多数情况下，你可以依赖编译器在你需要添加显式生命周期标注时给出提示。

## 参考资料 (References)

- [std::vec::Vec::iter](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.iter)
- [std::slice::Iter](https://doc.rust-lang.org/std/slice/struct.Iter.html)
- [Lifetime elision rules](https://doc.rust-lang.org/reference/lifetime-elision.html)

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/06_lifetimes.md)
