# 派生宏 (Derive macros)

为 `Ticket` 实现 `PartialEq` 是不是有点繁琐？
你必须手动比较结构体的每一个字段。

## 解构语法 (Destructuring syntax)

更何况，这种实现很脆弱：如果结构体定义发生变化（例如新加了一个字段），你必须记得去更新 `PartialEq` 的实现。

你可以通过把结构体**解构 (destructure)** 为它的各个字段来缓解这种风险：

```rust
impl PartialEq for Ticket {
    fn eq(&self, other: &Self) -> bool {
        let Ticket {
            title,
            description,
            status,
        } = self;
        // [...]
    }
}
```

如果 `Ticket` 的定义变了，编译器会报错，抱怨你的解构不再是穷尽 (exhaustive) 的。\
你也可以重命名结构体字段，避免变量遮蔽 (variable shadowing)：

```rust
impl PartialEq for Ticket {
    fn eq(&self, other: &Self) -> bool {
        let Ticket {
            title,
            description,
            status,
        } = self;
        let Ticket {
            title: other_title,
            description: other_description,
            status: other_status,
        } = other;
        // [...]
    }
}
```

解构是工具箱里好用的一招，但还有一种更便利的方式：**派生宏 (derive macro)**。

## 宏 (Macros)

你在前面的练习中已经接触过几个宏：

- `assert_eq!` 和 `assert!`，在测试用例中
- `println!`，向控制台打印

Rust 宏是**代码生成器 (code generator)**。\
它们根据你提供的输入生成新的 Rust 代码，生成的代码会和你程序的其余部分一起被编译。某些宏内建在 Rust 标准库中，但你也可以自己写宏。本课程不会让你自己写宏，但下面的["进一步阅读"](#进一步阅读)部分有不错的指引。

### 检视 (Inspection)

某些 IDE 允许你展开宏来查看生成的代码。如果做不到，你可以用 [`cargo-expand`](https://github.com/dtolnay/cargo-expand)。

### 派生宏 (Derive macros)

**派生宏 (derive macro)** 是 Rust 宏的一个特殊类别。它以**属性 (attribute)** 的形式标注在结构体上。

```rust
#[derive(PartialEq)]
struct Ticket {
    title: String,
    description: String,
    status: String
}
```

派生宏用来自动化为自定义类型实现常见的（且"显而易见"的）特质。
在上面的例子中，`Ticket` 自动实现了 `PartialEq` 特质。
如果你展开宏，会看到生成的代码与你手动写出的功能等价，只是读起来稍显冗长：

```rust
#[automatically_derived]
impl ::core::cmp::PartialEq for Ticket {
    #[inline]
    fn eq(&self, other: &Ticket) -> bool {
        self.title == other.title 
            && self.description == other.description
            && self.status == other.status
    }
}
```

只要可能，编译器会鼓励你使用派生 (derive) 来实现特质。

## 进一步阅读

- [The little book of Rust macros](https://veykril.github.io/tlborm/)
- [Proc macro workshop](https://github.com/dtolnay/proc-macro-workshop)

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/04_derive.md)
