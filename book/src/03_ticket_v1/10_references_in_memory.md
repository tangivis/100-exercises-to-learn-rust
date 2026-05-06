# 引用 (References)

那么 `&String` 或 `&mut String` 这样的引用呢？它们在内存中是如何表示的？

Rust 中大多数引用[^fat]在内存中都表示为指向一个内存位置的指针。\
因此它们的大小与指针大小相同，也就是一个 `usize`。

你可以使用 `std::mem::size_of` 来验证：

```rust
assert_eq!(std::mem::size_of::<&String>(), 8);
assert_eq!(std::mem::size_of::<&mut String>(), 8);
```

具体来说，`&String` 是一个指向存储 `String` 元数据 (metadata) 的内存位置的指针。\
如果你运行下面这段代码：

```rust
let s = String::from("Hey");
let r = &s;
```

内存中你会看到类似这样的布局：

```
           --------------------------------------
           |                                    |
      +----v----+--------+----------+      +----|----+
Stack | pointer | length | capacity |      | pointer |
      |  |      |   3    |    5     |      |         |
      +--|  ----+--------+----------+      +---------+
         |          s                           r
         |
         v
       +---+---+---+---+---+
Heap   | H | e | y | ? | ? |
       +---+---+---+---+---+
```

如果你愿意这么想：它是一个指向"指向堆上数据的指针"的指针。
`&mut String` 也是一样。

## 不是所有指针都指向堆 (Not all pointers point to the heap)

上面的例子应该能澄清一件事：并不是所有指针都指向堆。\
它们仅仅指向某个内存位置——_可能_在堆上，但不一定。

[^fat]: 在课程的[后面](../04_traits/06_str_slice.md)我们会讨论**胖指针 (fat pointer)**，
也就是带额外元数据的指针。顾名思义，它们比本章讨论的指针更大——后者也叫**瘦指针 (thin pointer)**。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/03_ticket_v1/10_references_in_memory.md)
