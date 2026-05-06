# 数组 (Arrays)

一谈到"工单管理 (ticket management)"，我们就要想办法存储 _多张_ 工单。
这又意味着我们需要思考集合 (collection)。具体来说，是同质 (homogeneous) 集合：
我们想存储同一类型的多个实例。

Rust 在这方面提供了什么？

## 数组 (Arrays)

第一种尝试是使用**数组 (array)**。\
Rust 中的数组是相同类型元素的固定大小集合。

下面是数组的定义方式：

```rust
// 数组类型语法：[ <类型> ; <元素数量> ]
let numbers: [u32; 3] = [1, 2, 3];
```

这创建了一个由 3 个整数组成的数组，初始化为 `1`、`2`、`3`。\
该数组的类型是 `[u32; 3]`，读作"长度为 3 的 `u32` 数组"。

如果数组所有元素相同，可以用更短的语法初始化：

```rust
// [ <值> ; <元素数量> ]
let numbers: [u32; 3] = [1; 3];
```

`[1; 3]` 创建一个三元素数组，元素全为 `1`。

### 访问元素 (Accessing elements)

可以用方括号访问数组元素：

```rust
let first = numbers[0];
let second = numbers[1];
let third = numbers[2];
```

索引必须是 `usize` 类型。\
跟 Rust 中其他东西一样，数组是**从 0 开始计索引 (zero-indexed)** 的。你之前在字符串切片以及元组/类元组变体的字段索引中见过这点。

### 越界访问 (Out-of-bounds access)

如果你尝试访问越界的元素，Rust 会 panic：

```rust
let numbers: [u32; 3] = [1, 2, 3];
let fourth = numbers[3]; // 这里会 panic
```

这是通过运行时的**边界检查 (bounds checking)** 强制执行的。它会带来少量性能开销，但这就是 Rust 防止缓冲区溢出 (buffer overflow) 的方式。\
某些情况下 Rust 编译器可以把边界检查优化掉，特别是涉及迭代器时——这点稍后会再细谈。

如果不想 panic，可以用 `get` 方法，它返回 `Option<&T>`：

```rust
let numbers: [u32; 3] = [1, 2, 3];
assert_eq!(numbers.get(0), Some(&1));
// 越界时返回 `None`，
// 而不是 panic。
assert_eq!(numbers.get(3), None);
```

### 性能 (Performance)

由于数组的大小在编译期已知，编译器可以把数组分配在栈上。
如果你运行下面的代码：

```rust
let numbers: [u32; 3] = [1, 2, 3];
```

会得到这样的内存布局：

```text
        +---+---+---+
Stack:  | 1 | 2 | 3 |
        +---+---+---+
```

换言之，数组的大小是 `std::mem::size_of::<T>() * N`，其中 `T` 是元素类型，`N` 是元素数量。\
你可以以 `O(1)` 时间访问和替换每个元素。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/01_arrays.md)
