# `TryFrom` 与 `TryInto`

上一章我们看过 [`From` 与 `Into` 特质](../04_traits/09_from.md)——
Rust 中处理**不会失败 (infallible)** 类型转换的习惯接口。\
但如果转换不一定成功呢？

我们对错误已经了解得够多，可以来看 `From` 与 `Into` 的**可能失败 (fallible)** 对应特质：`TryFrom` 与 `TryInto`。

## `TryFrom` 与 `TryInto`

`TryFrom` 与 `TryInto` 都定义在 `std::convert` 模块下，跟 `From`、`Into` 一样：

```rust
pub trait TryFrom<T>: Sized {
    type Error;
    fn try_from(value: T) -> Result<Self, Self::Error>;
}

pub trait TryInto<T>: Sized {
    type Error;
    fn try_into(self) -> Result<T, Self::Error>;
}
```

`From`/`Into` 与 `TryFrom`/`TryInto` 的主要区别在于后者返回 `Result` 类型。\
这允许转换失败，返回错误而不是 panic。

## `Self::Error`

`TryFrom` 与 `TryInto` 都有一个关联类型 `Error`。
这允许每个实现指定自己的错误类型，理想情况下选择最适合该转换的错误类型。

`Self::Error` 是一种引用特质本身定义的关联类型 `Error` 的方式。

## 对偶性 (Duality)

跟 `From` 与 `Into` 一样，`TryFrom` 与 `TryInto` 是对偶特质。\
如果你为某个类型实现了 `TryFrom`，就免费得到 `TryInto`。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/13_try_from.md)
