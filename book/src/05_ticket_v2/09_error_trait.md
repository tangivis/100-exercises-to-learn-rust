# Error 特质 (Error trait)

## 错误报告 (Error reporting)

在前一个练习中，你需要解构 `TitleError` 变体来取出错误消息，并把它传给 `panic!` 宏。\
这是一个（粗糙的）**错误报告 (error reporting)** 例子：把错误类型转换成可以呈现给用户、运维人员或开发者的表示。

让每个 Rust 开发者都各自琢磨一套错误报告策略并不实际：既浪费时间、跨项目时也不能很好地组合。
所以 Rust 提供了 `std::error::Error` 特质。

## `Error` 特质 (The `Error` trait)

`Result` 中 `Err` 变体的类型没有限制，但好的实践是使用一个实现了 `Error` 特质的类型。
`Error` 是 Rust 错误处理体系的基石：

```rust
// `Error` 特质的略简化定义
pub trait Error: Debug + Display {}
```

你可能还记得来自 [`From` 特质](../04_traits/09_from.md#父特质--子特质-supertrait--subtrait) 的 `:` 语法——它用来指定**父特质 (supertrait)**。
对 `Error` 来说，父特质有两个：`Debug` 和 `Display`。一个类型若想实现 `Error`，就也必须实现 `Debug` 和 `Display`。

## `Display` 与 `Debug`

我们[在前一个练习中](../04_traits/04_derive.md)已经接触过 `Debug` 特质——`assert_eq!` 在断言失败时用它来显示比较的变量值。

从"机制"上讲，`Display` 与 `Debug` 是相同的——它们编码了一个类型应该怎么转换为类似字符串的表示：

```rust
// `Debug`
pub trait Debug {
    fn fmt(&self, f: &mut Formatter<'_>) -> Result<(), Error>;
}

// `Display`
pub trait Display {
    fn fmt(&self, f: &mut Formatter<'_>) -> Result<(), Error>;
}
```

差别在于 _目的_：`Display` 返回面向"终端用户 (end-users)"的表示，
而 `Debug` 提供更适合开发者和运维的低层表示。\
这就是为什么 `Debug` 可以用 `#[derive(Debug)]` 属性自动实现，而 `Display` 则**必须**手动实现。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/09_error_trait.md)
