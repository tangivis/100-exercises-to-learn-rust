# 实现特质 (Implementing traits)

当一个类型定义在另一个 crate 中（例如来自 Rust 标准库的 `u32`），你不能直接为它定义新方法。如果你尝试：

```rust
impl u32 {
    fn is_even(&self) -> bool {
        self % 2 == 0
    }
}
```

编译器会抱怨：

```text
error[E0390]: cannot define inherent `impl` for primitive types
  |
1 | impl u32 {
  | ^^^^^^^^
  |
  = help: consider using an extension trait instead
```

## 扩展特质 (Extension trait)

**扩展特质 (extension trait)** 是一种特质，其主要目的是给外部类型（例如 `u32`）附加新方法。
这正是你在前一个练习中部署的模式：定义 `IsEven` 特质，再为 `i32` 和 `u32` 实现它。然后只要 `IsEven` 在作用域中，你就可以在这些类型上调用 `is_even`。

```rust
// 把特质引入作用域
use my_library::IsEven;

fn main() {
    // 在实现了它的类型上调用方法
    if 4.is_even() {
        // [...]
    }
}
```

## 只能有一个实现 (One implementation)

你能写的特质实现是有限制的。\
最简单、最直接的限制是：在同一个 crate 中，你不能为同一类型实现同一个特质两次。

例如：

```rust
trait IsEven {
    fn is_even(&self) -> bool;
}

impl IsEven for u32 {
    fn is_even(&self) -> bool {
        true
    }
}

impl IsEven for u32 {
    fn is_even(&self) -> bool {
        false
    }
}
```

编译器会拒绝它：

```text
error[E0119]: conflicting implementations of trait `IsEven` for type `u32`
   |
5  | impl IsEven for u32 {
   | ------------------- first implementation here
...
11 | impl IsEven for u32 {
   | ^^^^^^^^^^^^^^^^^^^ conflicting implementation for `u32`
```

当对一个 `u32` 值调用 `IsEven::is_even` 时，应当使用哪个特质实现不能存在歧义，因此只能存在一个。

## 孤儿规则 (Orphan rule)

涉及多个 crate 时情况更微妙。
具体来说，下列条件至少要有一个为真：

- 该特质定义在当前 crate 中
- 实现者类型定义在当前 crate 中

这就是 Rust 的**孤儿规则 (orphan rule)**。它的目标是让方法解析过程不出现歧义。

设想下面的情形：

- crate `A` 定义了 `IsEven` 特质
- crate `B` 为 `u32` 实现了 `IsEven`
- crate `C` 为 `u32` 提供了（与 `B` 不同的）`IsEven` 实现
- crate `D` 同时依赖 `B` 和 `C`，并调用 `1.is_even()`

应当使用哪个实现？是 `B` 中定义的那个？还是 `C` 中定义的那个？\
没有好答案，因此孤儿规则被定义出来以阻止这种情形发生。
有了孤儿规则，crate `B` 和 crate `C` 都将无法编译。

## 进一步阅读

- 上述孤儿规则其实有一些注意事项和例外。
  如果你想熟悉它的细节，请查看[参考手册](https://doc.rust-lang.org/reference/items/implementations.html#trait-implementation-coherence)。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/02_orphan_rule.md)
