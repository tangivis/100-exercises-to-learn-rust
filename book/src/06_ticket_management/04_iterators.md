# 迭代 (Iteration)

最早的练习里，你已经了解到 Rust 允许你用 `for` 循环遍历集合。
当时我们看的是范围（例如 `0..5`），但同样的规则也适用于像数组和向量这样的集合。

```rust
// 适用于 `Vec`
let v = vec![1, 2, 3];
for n in v {
    println!("{}", n);
}

// 也适用于数组
let a: [u32; 3] = [1, 2, 3];
for n in a {
    println!("{}", n);
}
```

是时候了解它在底层是如何工作的了。

## `for` 的脱糖 (`for` desugaring)

每当你在 Rust 里写 `for` 循环时，编译器会把它 _脱糖 (desugar)_ 成下面这样的代码：

```rust
let mut iter = IntoIterator::into_iter(v);
loop {
    match iter.next() {
        Some(n) => {
            println!("{}", n);
        }
        None => break,
    }
}
```

`loop` 是除 `for` 和 `while` 之外的另一种循环构造。\
`loop` 块会无限运行，除非你显式 `break`。

## `Iterator` 特质 (`Iterator` trait)

前面代码片段中的 `next` 方法来自 `Iterator` 特质。
`Iterator` 特质定义在 Rust 标准库中，为能够产生一系列值的类型提供共享接口：

```rust
trait Iterator {
    type Item;
    fn next(&mut self) -> Option<Self::Item>;
}
```

`Item` 关联类型 (associated type) 指定了迭代器产生的值的类型。

`next` 返回序列中的下一个值。\
如果有值可返回则返回 `Some(value)`，没有则返回 `None`。

注意：迭代器返回 `None` 不保证它已经耗尽。这只在迭代器实现了（更严格的）[`FusedIterator`](https://doc.rust-lang.org/std/iter/trait.FusedIterator.html) 特质时才能保证。

## `IntoIterator` 特质 (`IntoIterator` trait)

并不是所有类型都实现了 `Iterator`，但很多类型可以转换成实现了 `Iterator` 的类型。\
这就是 `IntoIterator` 特质的用武之地：

```rust
trait IntoIterator {
    type Item;
    type IntoIter: Iterator<Item = Self::Item>;
    fn into_iter(self) -> Self::IntoIter;
}
```

`into_iter` 方法消费原值，返回它的元素上的迭代器。\
一个类型只能有一个 `IntoIterator` 实现：`for` 该脱糖成什么不能有歧义。

一个细节：每个实现了 `Iterator` 的类型也自动实现了 `IntoIterator`。
它们的 `into_iter` 直接返回自身！

## 边界检查 (Bounds checks)

迭代有一个不错的副作用：按设计你不会越界。\
这允许 Rust 在生成的机器码中去掉边界检查，使迭代更快。

换句话说，

```rust
let v = vec![1, 2, 3];
for n in v {
    println!("{}", n);
}
```

通常比

```rust
let v = vec![1, 2, 3];
for i in 0..v.len() {
    println!("{}", v[i]);
}
```

更快。

这条规则有例外：编译器有时能证明你没有越界，即使是手动索引也能去掉边界检查。但总的来说，能用迭代就别用索引。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/04_iterators.md)
