# 参数位置上的 `impl Trait` (`impl Trait` in argument position)

上一节我们看到 `impl Trait` 可以用于返回一个类型而不指明其名字。\
同样的语法也可以用在**参数位置 (argument position)**：

```rust
fn print_iter(iter: impl Iterator<Item = i32>) {
    for i in iter {
        println!("{}", i);
    }
}
```

`print_iter` 接收一个 `i32` 的迭代器，并打印每个元素。\
当用在**参数位置**时，`impl Trait` 等价于带特质约束的泛型参数：

```rust
fn print_iter<T>(iter: T) 
where
    T: Iterator<Item = i32>
{
    for i in iter {
        println!("{}", i);
    }
}
```

## 缺点 (Downsides)

经验法则：在参数位置，优先使用泛型而非 `impl Trait`。\
泛型允许调用方用 turbofish 语法 (`::<>`) 显式指定参数的类型，这对消歧很有用。`impl Trait` 不行。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/09_impl_trait_2.md)
