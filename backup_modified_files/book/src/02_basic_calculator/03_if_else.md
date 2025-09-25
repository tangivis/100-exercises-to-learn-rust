# 控制流 (Control flow)（第一部分）

我们到目前为止编写的程序都非常简单。\
指令序列从上到下执行，仅此而已。

是时候引入一些**分支 (branching)** 了。

## `if` 语句

`if` 关键字用于仅在条件为真时执行代码块。

下面是一个简单的例子：

```rust
let number = 3;
if number < 5 {
    println!("`number` 小于 5");
}
```

这个程序会打印 `number is smaller than 5`，因为条件 `number < 5` 为真。

### `else` 语句

与大多数编程语言一样，Rust 支持可选的 `else` 分支，当 `if` 表达式中的条件为假时执行代码块。\
例如：

```rust
let number = 3;

if number < 5 {
    println!("`number` 小于 5");
} else {
    println!("`number` 大于或等于 5");
}
```

### `else if` 语句

当你有多个 `if` 表达式，一个嵌套在另一个里面时，你的代码会越来越向右偏移。

```rust
let number = 3;

if number < 5 {
    println!("`number` 小于 5");
} else {
    if number >= 3 {
        println!("`number` 大于或等于 3，但小于 5");
    } else {
        println!("`number` 小于 3");
    }
}
```

你可以使用 `else if` 关键字将多个 `if` 表达式组合成一个：

```rust
let number = 3;

if number < 5 {
    println!("`number` 小于 5");
} else if number >= 3 {
    println!("`number` 大于或等于 3，但小于 5");
} else {
    println!("`number` 小于 3");
}
```

## 布尔值 (Booleans)

`if` 表达式中的条件必须是 `bool` 类型，即**布尔值 (boolean)**。\
布尔值与整数一样，是 Rust 中的基本类型。

布尔值可以有两个值之一：`true` 或 `false`。

### 没有真值或假值 (No truthy or falsy values)

如果 `if` 表达式中的条件不是布尔值，你会得到一个编译错误 (compilation error)。

例如，下面的代码将无法编译：

```rust
let number = 3;
if number {
    println!("`number` 不为零");
}
```

你会得到以下编译错误：

```text
error[E0308]: 类型不匹配
 --> src/main.rs:3:8
  |
3 |     if number {
  |        ^^^^^^ 期望 `bool`，找到整数
```

这遵循了 Rust 关于类型强制的理念：没有从非布尔类型到布尔类型的自动转换。\
Rust 没有像 JavaScript 或 Python 那样的**真值 (truthy)** 或**假值 (falsy)** 概念。\
你必须明确指定要检查的条件。

### 比较运算符 (Comparison operators)

使用比较运算符为 `if` 表达式构建条件是很常见的。\
以下是 Rust 中处理整数时可用的比较运算符：

- `==`：等于
- `!=`：不等于
- `<`：小于
- `>`：大于
- `<=`：小于或等于
- `>=`：大于或等于

## `if/else` 是表达式 (expressions)

在 Rust 中，`if` 表达式是**表达式 (expression)**，而不是语句：它们返回一个值。\
这个值可以赋给变量或在其他表达式中使用。例如：

```rust
let number = 3;
let message = if number < 5 {
    "小于 5"
} else {
    "大于或等于 5"
};
```

在上面的例子中，`if` 的每个分支都评估为一个字符串字面量 (string literal)，
然后将其赋值给 `message` 变量。\
唯一的要求是两个 `if` 分支必须返回相同的类型。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/02_basic_calculator/03_if_else.md)
