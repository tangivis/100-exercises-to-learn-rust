# 特质 (Traits)

让我们再次看看 `Ticket` 类型：

```rust
pub struct Ticket {
    title: String,
    description: String,
    status: String,
}
```

到目前为止，我们的所有测试都是基于 `Ticket` 的字段进行断言的。

```rust
assert_eq!(ticket.title(), "A new title");
```

如果我们想直接比较两个 `Ticket` 实例呢？

```rust
let ticket1 = Ticket::new(/* ... */);
let ticket2 = Ticket::new(/* ... */);
ticket1 == ticket2
```

编译器会拦下我们：

```text
error[E0369]: binary operation `==` cannot be applied to type `Ticket`
  --> src/main.rs:18:13
   |
18 |     ticket1 == ticket2
   |     ------- ^^ ------- Ticket
   |     |
   |     Ticket
   |
note: an implementation of `PartialEq` might be missing for `Ticket`
```

`Ticket` 是一个新类型。开箱即用时，**它身上没有任何行为**。\
Rust 不会因为它包含了 `String` 字段就魔法般地推断出该如何比较两个 `Ticket` 实例。

不过 Rust 编译器朝着正确方向给了我们提示：它建议我们可能缺少了 `PartialEq` 的实现。`PartialEq` 就是一个**特质 (trait)**！

## 什么是特质？(What are traits?)

特质 (trait) 是 Rust 定义**接口 (interface)** 的方式。\
特质定义了一组方法，类型必须实现它们才能满足该特质的契约 (contract)。

### 定义一个特质 (Defining a trait)

特质定义的语法如下：

```rust
trait <特质名> {
    fn <方法名>(<参数>) -> <返回类型>;
}
```

例如，我们可以定义一个名为 `MaybeZero` 的特质，要求实现者定义一个 `is_zero` 方法：

```rust
trait MaybeZero {
    fn is_zero(self) -> bool;
}
```

### 实现一个特质 (Implementing a trait)

为类型实现特质要使用 `impl` 关键字，跟我们为类型定义普通[^inherent]方法时一样，只是语法略有不同：

```rust
impl <特质名> for <类型名> {
    fn <方法名>(<参数>) -> <返回类型> {
        // 方法体
    }
}
```

例如，给一个自定义数字类型 `WrappingU32` 实现 `MaybeZero` 特质：

```rust
pub struct WrappingU32 {
    inner: u32,
}

impl MaybeZero for WrappingU32 {
    fn is_zero(self) -> bool {
        self.inner == 0
    }
}
```

### 调用特质方法 (Invoking a trait method)

要调用特质的方法，跟调用普通方法一样使用 `.` 运算符：

```rust
let x = WrappingU32 { inner: 5 };
assert!(!x.is_zero());
```

要调用特质方法，必须满足两个条件：

- 类型实现了该特质。
- 该特质必须在作用域 (scope) 中。

为满足后者，你可能需要为该特质添加一个 `use` 语句：

```rust
use crate::MaybeZero;
```

下列情况则不需要 `use`：

- 特质定义在调用所在的同一模块中。
- 特质定义在标准库的**预导入 (prelude)** 中。
  预导入是一组在每个 Rust 程序中都会自动导入的特质和类型。
  就好像每个 Rust 模块开头都隐式加了一句 `use std::prelude::*;`。

你可以在 [Rust 文档](https://doc.rust-lang.org/std/prelude/index.html)中查看预导入中的特质和类型列表。

[^inherent]: 不通过特质、直接定义在类型上的方法也叫**固有方法 (inherent method)**。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/01_trait.md)
