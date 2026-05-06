# 循环，第二部分：`for` (Loops, part 2)

手动递增一个计数器变量有点繁琐。这种模式还非常常见！\
为了让事情更简单，Rust 提供了一种更简洁的方式来遍历一个值序列：`for` 循环。

## `for` 循环

`for` 循环是一种为迭代器 (iterator)[^iterator] 中的每个元素执行代码块的方式。

通用语法如下：

```rust
for <element> in <iterator> {
    // 要执行的代码
}
```

## 范围 (Ranges)

Rust 标准库 (standard library) 提供了**范围 (range)** 类型，可以用来遍历一个数字序列[^weird-ranges]。

例如，如果我们想对 1 到 5 之间的数字求和：

```rust
let mut sum = 0;
for i in 1..=5 {
    sum += i;
}
```

每次循环运行时，`i` 都会在执行代码块之前被赋值为范围 (range) 中的下一个值。

Rust 中有五种范围 (range)：

- `1..5`：一个（半开）范围。它包含从 1 到 4 的所有数字，不包含最后一个值 5。
- `1..=5`：一个闭合范围 (inclusive range)。它包含从 1 到 5 的所有数字，包括最后一个值 5。
- `1..`：一个开放式范围 (open-ended range)。它包含从 1 到无穷大的所有数字（实际上是到该整数类型的最大值）。
- `..5`：一个从该整数类型的最小值开始、到 4 结束的范围。它不包含最后一个值 5。
- `..=5`：一个从该整数类型的最小值开始、到 5 结束的范围。它包含最后一个值 5。

你可以在 `for` 循环中使用前三种范围 (range)，因为它们显式指定了起始点。后两种范围 (range) 用于其他场合，我们之后会介绍。

范围 (range) 的端点不一定要是整数字面量 (integer literal)——它们也可以是变量或表达式 (expression)！

例如：

```rust
let end = 5;
let mut sum = 0;

for i in 1..(end + 1) {
    sum += i;
}
```

## 进一步阅读

- [`for` 循环文档](https://doc.rust-lang.org/std/keyword.for.html)

[^iterator]: 在课程的后面部分，我们会精确地定义什么算作"迭代器 (iterator)"。
现在，把它当作一个你可以遍历的值序列即可。
[^weird-ranges]: 你也可以将范围 (range) 用于其他类型（例如字符和 IP 地址），
但在日常的 Rust 编程中，整数无疑是最常见的情况。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/02_basic_calculator/07_for.md)
