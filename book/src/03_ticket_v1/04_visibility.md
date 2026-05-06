# 可见性 (Visibility)

当你开始把代码拆分成多个模块时，就需要考虑**可见性 (visibility)** 了。
可见性决定了哪些代码区域（你的或别人的）可以访问某个实体——无论它是结构体、函数、字段还是其他东西。

## 默认私有 (Private by default)

默认情况下，Rust 中的所有内容都是**私有 (private)** 的。\
私有实体只能在以下范围内访问：

1. 在它定义所在的同一模块内，或
2. 该模块的某个子模块内

我们在前面的练习中已经大量用到这点：

- `create_todo_ticket` 能够运行（在你加上 `use` 语句之后），是因为 `helpers` 是 crate 根的子模块，而 `Ticket` 就定义在 crate 根中。因此 `create_todo_ticket` 即使在 `Ticket` 是私有的情况下也能毫无问题地访问到它。
- 我们所有的单元测试都定义在被测代码的子模块中，因此它们能不受限制地访问任何东西。

## 可见性修饰符 (Visibility modifiers)

你可以用**可见性修饰符 (visibility modifier)** 来修改实体的默认可见性。\
一些常见的可见性修饰符是：

- `pub`：将实体设为**公有 (public)**，即可以从定义所在模块的外部访问，潜在地包括其他 crate。
- `pub(crate)`：在同一 **crate** 内公开，但不暴露给 crate 外部。
- `pub(super)`：在父模块中公开。
- `pub(in path::to::module)`：在指定的模块中公开。

这些修饰符可以用在模块、结构体、函数、字段等之上。
例如：

```rust
pub struct Configuration {
    pub(crate) version: u32,
    active: bool,
}
```

`Configuration` 是公有的，但你只能在同一 crate 内访问 `version` 字段。
而 `active` 字段是私有的，只能在同一模块或其子模块中访问。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/03_ticket_v1/04_visibility.md)
