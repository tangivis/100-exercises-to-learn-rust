# 复制值，第二部分 (Copying values, pt. 2)

让我们看与之前相同的例子，但稍作改动：用 `u32` 代替 `String`。

```rust
fn consumer(s: u32) { /* */ }

fn example() {
     let s: u32 = 5;
     consumer(s);
     let t = s + 1;
}
```

它能编译通过，没有错误！这是怎么回事？`String` 和 `u32` 之间有什么区别，让后者不需要 `.clone()` 就能用？

## `Copy`

`Copy` 是 Rust 标准库中定义的另一个特质：

```rust
pub trait Copy: Clone { }
```

它是一个标记特质 (marker trait)，跟 `Sized` 一样。

如果一个类型实现了 `Copy`，就不需要调用 `.clone()` 来创建新实例：
Rust 会**隐式 (implicitly)** 替你完成。\
`u32` 就是实现了 `Copy` 的类型，这就是为什么上面的例子能无错地编译：
当 `consumer(s)` 被调用时，Rust 通过对 `s` 进行**逐位复制 (bitwise copy)** 来创建一个新的 `u32` 实例，然后把这个新实例传给 `consumer`。这一切都在幕后发生，你什么都不用做。

## 什么样的类型可以 `Copy`？(What can be `Copy`?)

`Copy` 不等于"自动克隆 (automatic cloning)"，但它确实意味着这一点。\
类型必须满足若干条件，才被允许实现 `Copy`。

首先，它必须实现 `Clone`，因为 `Copy` 是 `Clone` 的子特质。
这是合理的：如果 Rust 能够 _隐式_ 创建该类型的新实例，那它也应当能通过调用 `.clone()` 来 _显式_ 创建。

但这还不够，还需要满足下面几个条件：

1. 该类型不管理任何 _额外的_ 资源（例如堆内存、文件句柄等），只占用 `std::mem::size_of` 那么多字节。
2. 该类型不是可变引用 (`&mut T`)。

如果两个条件都满足，那么 Rust 就可以安全地通过对原实例进行**逐位复制 (bitwise copy)** 来创建一个新实例——这通常被称为 `memcpy` 操作（得名于 C 标准库中执行逐位复制的函数）。

### 案例分析 1：`String` (Case study 1: `String`)

`String` 不实现 `Copy`。\
为什么？因为它管理着额外资源：存储字符串数据的、堆上分配的内存缓冲区。

让我们假设 Rust 允许 `String` 实现 `Copy`。\
那当通过对原实例进行逐位复制创建新的 `String` 实例时，原实例和新实例会指向同一块内存缓冲区：

```text
              s                                 copied_s
+---------+--------+----------+      +---------+--------+----------+
| pointer | length | capacity |      | pointer | length | capacity |
|  |      |   5    |    5     |      |  |      |   5    |    5     |
+--|------+--------+----------+      +--|------+--------+----------+
   |                                    |
   |                                    |
   v                                    |
 +---+---+---+---+---+                  |
 | H | e | l | l | o |                  |
 +---+---+---+---+---+                  |
   ^                                    |
   |                                    |
   +------------------------------------+
```

这是有害的！
两个 `String` 实例在离开作用域时都会尝试释放这同一块内存缓冲区，导致双重释放 (double-free) 错误。
你也可以创建两个不同的 `&mut String` 引用指向同一块内存缓冲区，这就违反了 Rust 的借用规则。

### 案例分析 2：`u32` (Case study 2: `u32`)

`u32` 实现了 `Copy`。事实上所有整数类型都实现了。\
整数"不过是"内存中表示该数字的那些字节，没有别的！
如果你复制这些字节，你就得到了另一个完全有效的整数实例。
不会出什么坏事，所以 Rust 允许它。

### 案例分析 3：`&mut u32` (Case study 3: `&mut u32`)

引入所有权 (ownership) 和可变借用 (mutable borrow) 时，我们清晰地讲过一条规则：任意时刻一个值只能存在 _一个_ 可变借用。\
这就是为什么 `&mut u32` 不实现 `Copy`，即使 `u32` 实现了。

如果 `&mut u32` 实现了 `Copy`，你就能创建多个指向同一个值的可变引用，并在多个地方同时修改它。
那就违反了 Rust 的借用规则！
所以，无论 `T` 是什么，`&mut T` 都不实现 `Copy`。

## 实现 `Copy` (Implementing `Copy`)

大多数情况下，你不需要手动实现 `Copy`。
直接派生即可，像这样：

```rust
#[derive(Copy, Clone)]
struct MyStruct {
    field: u32,
}
```

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/12_copy.md)
