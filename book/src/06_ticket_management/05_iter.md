# `.iter()`

`IntoIterator` 会**消费 (consume)** `self` 来创建迭代器。

这有它的好处：你从迭代器拿到的是**拥有所有权 (owned)** 的值。
例如：在 `Vec<Ticket>` 上调用 `.into_iter()` 会得到一个返回 `Ticket` 值的迭代器。

这也有缺点：调用 `.into_iter()` 之后你就不能再使用原集合了。
很多时候你想在不消费集合的前提下遍历它，看到值的**引用 (reference)**。
对 `Vec<Ticket>` 来说，你想要的是遍历 `&Ticket` 值。

大多数集合都暴露了一个 `.iter()` 方法，返回对集合元素引用的迭代器。
例如：

```rust
let numbers: Vec<u32> = vec![1, 2];
// 这里 `n` 的类型是 `&u32`
for n in numbers.iter() {
    // [...]
}
```

这种模式可以通过为**对集合的引用**实现 `IntoIterator` 来简化。
上面的例子中就是 `&Vec<Ticket>`。\
标准库这样做了，所以下面的代码可以工作：

```rust
let numbers: Vec<u32> = vec![1, 2];
// 这里 `n` 的类型是 `&u32`
// 我们没有显式调用 `.iter()`
// 在 `for` 循环中使用 `&numbers` 就够了
for n in &numbers {
    // [...]
}
```

习惯上同时提供两种方式：

- 为对集合的引用实现 `IntoIterator`。
- 一个返回对集合元素引用的迭代器的 `.iter()` 方法。

前者在 `for` 循环里方便，后者更显式，可以在其他场景使用。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/05_iter.md)
