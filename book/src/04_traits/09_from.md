# `From` 与 `Into`

让我们回到字符串之旅开始的地方：

```rust
let ticket = Ticket::new(
    "A title".into(), 
    "A description".into(), 
    "To-Do".into()
);
```

我们现在已经知道得够多，可以开始拆解这里的 `.into()` 到底在做什么了。

## 问题 (The problem)

这是 `new` 方法的签名：

```rust
impl Ticket {
    pub fn new(
        title: String, 
        description: String, 
        status: String
    ) -> Self {
        // [...]
    }
}
```

我们也已经看到字符串字面量（如 `"A title"`）的类型是 `&str`。\
这里有类型不匹配：期望的是 `String`，但我们手上的是 `&str`。
这次没有什么神奇的强制转换 (coercion) 来救场；我们需要**执行一次转换 (perform a conversion)**。

## `From` 与 `Into`

Rust 标准库在 `std::convert` 模块中定义了两个用于**不会失败的转换 (infallible conversion)** 的特质：`From` 和 `Into`。

```rust
pub trait From<T>: Sized {
    fn from(value: T) -> Self;
}

pub trait Into<T>: Sized {
    fn into(self) -> T;
}
```

这些特质定义展示了几个我们之前没见过的概念：**父特质 (supertrait)** 和**隐式特质约束 (implicit trait bound)**。
我们先把它们拆开来看。

### 父特质 / 子特质 (Supertrait / Subtrait)

`From: Sized` 这个语法意味着 `From` 是 `Sized` 的**子特质 (subtrait)**：任何实现了 `From` 的类型也必须实现 `Sized`。
反过来你也可以说：`Sized` 是 `From` 的**父特质 (supertrait)**。

### 隐式特质约束 (Implicit trait bounds)

每当你拥有泛型类型参数时，编译器会隐式假设它是 `Sized` 的。

例如：

```rust
pub struct Foo<T> {
    inner: T,
}
```

实际上等价于：

```rust
pub struct Foo<T: Sized> 
{
    inner: T,
}
```

对于 `From<T>`，这条特质定义等价于：

```rust
pub trait From<T: Sized>: Sized {
    fn from(value: T) -> Self;
}
```

换句话说，`T` _以及_ 实现 `From<T>` 的类型都必须是 `Sized` 的，
即使前一条约束是隐式的。

### 否定特质约束 (Negative trait bounds)

你可以通过**否定特质约束 (negative trait bound)** 来取消隐式的 `Sized` 约束：

```rust
pub struct Foo<T: ?Sized> {
    //            ^^^^^^^
    //            这是一个否定特质约束
    inner: T,
}
```

这个语法读作"`T` 可能是 `Sized`，也可能不是"，并且允许你把 `T` 绑到 DST 上（例如 `Foo<str>`）。这是一个特殊情况：否定特质约束是 `Sized` 专属的，不能用在其他特质上。

## `&str` 转 `String` (`&str` to `String`)

在 [`std` 文档](https://doc.rust-lang.org/std/convert/trait.From.html#implementors)中你可以看到哪些 `std` 类型实现了 `From` 特质。\
你会发现 `String` 实现了 `From<&str> for String`。因此我们可以写：

```rust
let title = String::from("A title");
```

不过我们一直主要在用 `.into()`。\
如果你查看 [`Into` 的实现者列表](https://doc.rust-lang.org/std/convert/trait.Into.html#implementors)，
你不会找到 `Into<String> for &str`。这是怎么回事？

`From` 与 `Into` 是**对偶的特质 (dual traits)**。\
具体来说，`Into` 通过一条**全覆盖实现 (blanket implementation)** 自动地为任何实现了 `From` 的类型实现：

```rust
impl<T, U> Into<U> for T
where
    U: From<T>,
{
    fn into(self) -> U {
        U::from(self)
    }
}
```

如果类型 `U` 实现了 `From<T>`，那么 `Into<U> for T` 会自动被实现。这就是为什么我们可以写 `let title = "A title".into();`。

## `.into()`

每当你看到 `.into()` 时，你正在见证一次类型之间的转换。\
但目标类型是什么呢？

大多数情况下，目标类型要么是：

- 由函数/方法的签名指定（例如上面例子里的 `Ticket::new`）
- 或在变量声明中通过类型注解指定（例如 `let title: String = "A title".into();`）

只要编译器能从上下文中无歧义地推断出目标类型，`.into()` 就会开箱即用。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/09_from.md)
