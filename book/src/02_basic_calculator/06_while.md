# 循环，第一部分：`while` (Loops, part 1)

你之前对 `factorial` 的实现被迫使用了递归 (recursion)。\
如果你来自函数式编程 (functional programming) 背景，这可能让你感觉很自然。
但如果你习惯了 C 或 Python 这类更偏命令式 (imperative) 的语言，可能会觉得有些奇怪。

让我们看看如何用**循环 (loop)** 来实现相同的功能。

## `while` 循环

`while` 循环是一种只要**条件 (condition)** 为真就持续执行代码块的方式。\
通用语法如下：

```rust
while <condition> {
    // 要执行的代码
}
```

例如，我们可能想要对 1 到 5 之间的数字求和：

```rust
let sum = 0;
let i = 1;
// "当 i 小于或等于 5 时"
while i <= 5 {
    // `+=` 是 `sum = sum + i` 的简写
    sum += i;
    i += 1;
}
```

这段代码会不断地给 `i` 加 1，并把 `i` 加到 `sum` 上，直到 `i` 不再小于或等于 5。

## `mut` 关键字

上面的例子按原样是无法编译的。你会得到类似这样的错误：

```text
error[E0384]: cannot assign twice to immutable variable `sum`
 --> src/main.rs:7:9
  |
2 |     let sum = 0;
  |         ---
  |         |
  |         first assignment to `sum`
  |         help: consider making this binding mutable: `mut sum`
...
7 |         sum += i;
  |         ^^^^^^^^ cannot assign twice to immutable variable

error[E0384]: cannot assign twice to immutable variable `i`
 --> src/main.rs:8:9
  |
3 |     let i = 1;
  |         -
  |         |
  |         first assignment to `i`
  |         help: consider making this binding mutable: `mut i`
...
8 |         i += 1;
  |         ^^^^^^ cannot assign twice to immutable variable
```

这是因为 Rust 中的变量默认是**不可变的 (immutable)**。\
一旦赋值后，你就不能再改变它们的值。

如果你希望允许修改，必须使用 `mut` 关键字将变量声明为**可变的 (mutable)**：

```rust
// `sum` 和 `i` 现在是可变的了！
let mut sum = 0;
let mut i = 1;

while i <= 5 {
    sum += i;
    i += 1;
}
```

这样就可以正常编译并运行了。

## 进一步阅读

- [`while` 循环文档](https://doc.rust-lang.org/std/keyword.while.html)

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/02_basic_calculator/06_while.md)
