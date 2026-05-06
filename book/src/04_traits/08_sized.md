# `Sized`

即便研究过解引用强制转换 (deref coercion)，`&str` 仍然有一些没暴露出来的细节。\
基于我们[前面关于内存布局的讨论](../03_ticket_v1/10_references_in_memory.md)，
本来合理的预期是 `&str` 在栈上表示为单个 `usize`——一个指针。但事实并非如此。`&str` 在指针旁还存了一些**元数据 (metadata)**：它指向的切片的长度。回到[前一节](06_str_slice.md)的例子：

```rust
let mut s = String::with_capacity(5);
s.push_str("Hello");
// 从 `String` 创建一个字符串切片引用，
// 跳过第一个字节。
let slice: &str = &s[1..];
```

在内存里，我们得到：

```text
                    s                              slice
      +---------+--------+----------+      +---------+--------+
Stack | pointer | length | capacity |      | pointer | length |
      |    |    |   5    |    5     |      |    |    |   4    |
      +----|----+--------+----------+      +----|----+--------+
           |        s                           |  
           |                                    |
           v                                    | 
         +---+---+---+---+---+                  |
Heap:    | H | e | l | l | o |                  |
         +---+---+---+---+---+                  |
               ^                                |
               |                                |
               +--------------------------------+
```

这是怎么回事？

## 动态尺寸类型 (Dynamically sized types)

`str` 是一种**动态尺寸类型 (dynamically sized type, DST)**。\
DST 的大小在编译期是未知的。每当你持有对 DST 的引用——例如 `&str`——它必须包含关于其所指数据的额外信息。这就是**胖指针 (fat pointer)**。\
在 `&str` 的情形下，它存储了它所指切片的长度。
我们会在课程后面看到更多 DST 的例子。

## `Sized` 特质 (The `Sized` trait)

Rust 的 `std` 库定义了一个名为 `Sized` 的特质。

```rust
pub trait Sized {
    // 这是个空特质，没有任何方法要实现。
}
```

如果一个类型的大小在编译期已知，它就是 `Sized` 的。换句话说，它不是 DST。

### 标记特质 (Marker traits)

`Sized` 是你接触到的第一个**标记特质 (marker trait)**。\
标记特质不要求实现任何方法，也不定义任何行为。
它只用来**标记 (mark)** 一个类型具有某些性质。
然后编译器利用这个标记来启用某些行为或优化。

### 自动特质 (Auto traits)

特别地，`Sized` 也是一个**自动特质 (auto trait)**。\
你不需要显式实现它，编译器会根据类型的定义自动为你实现。

### 例子 (Examples)

到目前为止我们见过的所有类型都是 `Sized`：`u32`、`String`、`bool` 等等。

`str`，正如刚才所见，不是 `Sized` 的。\
不过 `&str` 是 `Sized` 的！我们在编译期就知道它的大小：两个 `usize`，一个用于指针，一个用于长度。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/08_sized.md)
