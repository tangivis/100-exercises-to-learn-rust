# 封装 (Encapsulation)

现在我们对模块 (module) 和可见性 (visibility) 有了基本的理解，让我们回到**封装 (encapsulation)** 这个话题。\
封装是指隐藏对象内部表示 (internal representation) 的实践。它最常见的用途是在对象状态上强制施加一些**不变量 (invariant)**。

回到我们的 `Ticket` 结构体：

```rust
struct Ticket {
    title: String,
    description: String,
    status: String,
}
```

如果所有字段都设为公有，就完全没有封装可言。\
你必须假设字段随时可能被修改，可以被设为类型允许的任何值。你无法排除工单标题为空、或者状态毫无意义的可能性。

要执行更严格的规则，我们必须把字段保持为私有[^newtype]。
然后我们提供公有方法来与 `Ticket` 实例交互。
这些公有方法负责维护我们的不变量（例如：标题不能为空）。

只要至少有一个字段是私有的，就不再可能用结构体实例化语法直接创建 `Ticket` 实例：

```rust
// 这无法工作！
let ticket = Ticket {
    title: "Build a ticket system".into(),
    description: "A Kanban board".into(),
    status: "Open".into()
};
```

你在前面关于可见性 (visibility) 的练习中已经见识过这一点。\
我们现在需要提供一个或多个公有的**构造器 (constructor)**——也就是可以从模块外部使用的、用来创建结构体实例的静态方法或函数。\
所幸我们已经有一个：[前一个练习](02_validation.md)中实现的 `Ticket::new`。

## 访问器方法 (Accessor methods)

总结一下：

- `Ticket` 的所有字段都是私有的
- 我们提供了公有构造器 `Ticket::new`，在创建时强制执行验证规则

这是个不错的开始，但还不够：除了创建 `Ticket`，我们还要与它交互。
但如果字段是私有的，怎么访问它们呢？

我们需要提供**访问器方法 (accessor method)**。\
访问器方法是公有方法，允许你读取结构体一个（或多个）私有字段的值。

Rust 没有像某些语言那样内建的方式来自动生成访问器方法。
你得自己写——它们就是普通的方法。

[^newtype]: 也可以选择细化（refine）字段的类型，这是一种我们[稍后](../05_ticket_v2/15_outro.md)会探索的技术。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/03_ticket_v1/05_encapsulation.md)
