# 内存布局 (Memory layout)

我们已经从操作层面看过了所有权 (ownership) 和引用 (reference)——能做什么、不能做什么。
现在是时候掀开盖子看看：聊聊**内存 (memory)** 吧。

## 栈与堆 (Stack and heap)

谈到内存时，你常常会听人提起**栈 (stack)** 和**堆 (heap)**。\
它们是程序用来存储数据的两种不同的内存区域。

我们先从栈讲起。

## 栈 (Stack)

**栈 (stack)** 是一种 **LIFO**（Last In, First Out，后进先出）数据结构。\
当你调用一个函数时，一个新的**栈帧 (stack frame)** 会被压到栈顶。这个栈帧用于存储函数的参数、局部变量以及一些"簿记 (bookkeeping)"值。\
当函数返回时，对应的栈帧会从栈上弹出[^stack-overflow]。

```text
+-----------------+
| frame for func1 |
+-----------------+
        |
        | 调用 func2
        v
+-----------------+
| frame for func2 |
+-----------------+
| frame for func1 |
+-----------------+
        |
        | func2 返回
        v
+-----------------+
| frame for func1 |
+-----------------+
```

从操作层面看，栈上的分配/释放**非常快**。\
我们总是从栈顶压入和弹出数据，因此不需要去搜索可用内存。
我们也不必担心碎片 (fragmentation)：栈是一整块连续 (contiguous) 的内存。

### Rust

Rust 经常把数据放在栈上。\
你的函数有一个 `u32` 类型的输入参数？那 32 位会放在栈上。\
你定义了一个 `i64` 类型的局部变量？那 64 位也会放在栈上。\
这一切运转良好，是因为这些整数的大小在编译期就已知，因此编译后的程序知道为它们在栈上保留多少空间。

### `std::mem::size_of`

你可以使用 [`std::mem::size_of`](https://doc.rust-lang.org/std/mem/fn.size_of.html) 函数来查看某个类型在栈上会占用多少空间。

例如对 `u8`：

```rust
// 这个奇怪的语法 (`::<u8>`) 我们之后再讲。
// 现在先忽略它。
assert_eq!(std::mem::size_of::<u8>(), 1);
```

结果是 1，这很合理，因为 `u8` 是 8 位长，也就是 1 字节。

[^stack-overflow]: 如果你有嵌套的函数调用，每个函数被调用时都把它的数据压到栈上，
但要等到最内层的函数返回时才会弹出。
如果嵌套调用太深，你可能会用尽栈空间——栈不是无限的！
这就叫 [**栈溢出 (stack overflow)**](https://en.wikipedia.org/wiki/Stack_overflow)。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/03_ticket_v1/08_stack.md)
