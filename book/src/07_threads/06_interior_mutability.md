# 内部可变性 (Interior mutability)

我们来稍微推敲一下 `Sender` 的 `send` 签名：

```rust
impl<T> Sender<T> {
    pub fn send(&self, t: T) -> Result<(), SendError<T>> {
        // [...]
    }
}
```

`send` 接受 `&self` 作为参数。\
但它显然在引发修改：往通道里加入了一条新消息。
更有意思的是 `Sender` 是可克隆的：我们可以让多个 `Sender` 实例从不同线程**同时**尝试修改通道状态。

这是我们用来构建 client-server 架构的关键性质。但它为什么有效？
难道这不违反 Rust 关于借用的规则吗？我们怎么能通过一个 _不可变_ 引用执行修改？

## 共享引用而非不可变引用 (Shared rather than immutable references)

我们引入借用检查器时，把 Rust 中的两种引用命名为：

- 不可变引用 (immutable references) `&T`
- 可变引用 (mutable references) `&mut T`

把它们叫作下列名字会更准确：

- 共享引用 (shared references) `&T`
- 独占引用 (exclusive references) `&mut T`

不可变/可变是一个对绝大多数情况都有效的心智模型，也是上手 Rust 时很好的一个。但它不是全貌，正如你刚刚看到的：`&T` 实际上并不保证它指向的数据不可变。\
不过别担心：Rust 仍然在信守它的承诺。
只是这些术语比表面看起来要更微妙。

## `UnsafeCell`

每当一个类型允许你通过共享引用修改数据时，你就在和**内部可变性 (interior mutability)** 打交道。

默认情况下，Rust 编译器假设共享引用是不可变的。它基于这个假设**优化你的代码**。\
编译器可以重排操作、缓存值，做各种把戏让你的代码更快。

你可以通过把数据包到 `UnsafeCell` 里告诉编译器"不，这个共享引用其实是可变的"。\
每次你看到一个允许内部可变性的类型，可以确定 `UnsafeCell` 牵涉其中——直接或间接地。\
借助 `UnsafeCell`、原始指针和 `unsafe` 代码，你可以通过共享引用修改数据。

不过要说清楚：`UnsafeCell` 不是允许你忽略借用检查器的魔法棒！\
`unsafe` 代码仍然受 Rust 关于借用与混叠 (aliasing) 规则的约束。
它是一种（高级）工具，用来构建那些其安全性无法直接通过 Rust 类型系统表达的**安全抽象 (safe abstractions)**。每次你使用 `unsafe` 关键字，等于在告诉编译器："我知道我在做什么，不会违反你的不变量，相信我。"

每次你调用一个 `unsafe` 函数，文档都会说明它的**安全前置条件 (safety preconditions)**：
执行其 `unsafe` 块要满足什么条件才安全。`UnsafeCell` 的可在 [`std` 文档](https://doc.rust-lang.org/std/cell/struct.UnsafeCell.html) 找到。

我们这门课不会直接使用 `UnsafeCell`，也不会写 `unsafe` 代码。
但了解它存在、为什么存在以及它跟你日常使用的 Rust 类型有何关系，是重要的。

## 关键例子 (Key examples)

我们看几个利用内部可变性的重要 `std` 类型。\
它们是你在 Rust 代码中相当常见的类型，特别是当你揭开你使用的库的盖子时。

### 引用计数 (Reference counting)

`Rc` 是一个引用计数指针。\
它包裹一个值，并跟踪有多少个对该值的引用存在。
当最后一个引用被丢弃时，该值被释放。\
被 `Rc` 包裹的值是不可变的：你只能得到对它的共享引用。

```rust
use std::rc::Rc;

let a: Rc<String> = Rc::new("My string".to_string());
// 字符串数据只有一个引用。
assert_eq!(Rc::strong_count(&a), 1);

// 调用 `clone` 不会复制字符串数据！
// 只是把 `Rc` 的引用计数加 1。
let b = Rc::clone(&a);
assert_eq!(Rc::strong_count(&a), 2);
assert_eq!(Rc::strong_count(&b), 2);
// ^ `a` 和 `b` 都指向同一份字符串数据
//   并共享同一个引用计数器。
```

`Rc` 内部用 `UnsafeCell` 来允许共享引用增减引用计数。

### `RefCell`

`RefCell` 是 Rust 中内部可变性最常见的例子之一。
它允许你在只持有 `RefCell` 的不可变引用时，仍能修改被 `RefCell` 包裹的值。

这是通过**运行时借用检查 (runtime borrow checking)** 实现的。
`RefCell` 在运行时跟踪它所包含值的引用数量（与类型）。
如果你尝试在已存在不可变借用时再做可变借用，程序会 panic，确保 Rust 的借用规则始终被强制执行。

```rust
use std::cell::RefCell;

let x = RefCell::new(42);

let y = x.borrow(); // 不可变借用
let z = x.borrow_mut(); // panic！已有活跃的不可变借用。
```

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/06_interior_mutability.md)
