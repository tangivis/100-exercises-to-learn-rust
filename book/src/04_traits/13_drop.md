# `Drop` 特质 (The `Drop` trait)

我们引入[析构函数 (destructor)](../03_ticket_v1/11_destructor.md)时提到过，`drop` 函数会：

1. 回收类型所占用的内存（即 `std::mem::size_of` 那么多字节）
2. 清理该值可能管理的任何额外资源（例如 `String` 的堆缓冲区）

第 2 步正是 `Drop` 特质登场的地方。

```rust
pub trait Drop {
    fn drop(&mut self);
}
```

`Drop` 特质给你一种机制，让你为自己的类型定义 _额外的_ 清理逻辑——超出编译器自动替你做的那部分。\
你放进 `drop` 方法里的代码会在值离开作用域时被执行。

## `Drop` 与 `Copy` (`Drop` and `Copy`)

谈到 `Copy` 特质时我们说过：如果一个类型管理了超出 `std::mem::size_of` 字节的额外资源，它就不能实现 `Copy`。

你可能会问：编译器怎么知道一个类型是否管理了额外资源？\
对：通过 `Drop` 特质的实现！\
如果你的类型有显式的 `Drop` 实现，编译器就会假定该类型附带了额外资源，从而不允许你实现 `Copy`。

```rust
// 这是一个单元结构体 (unit struct)，即没有字段的结构体。
#[derive(Clone, Copy)]
struct MyType;

impl Drop for MyType {
    fn drop(&mut self) {
       // 这里我们不需要做什么，
       // 有一个"空"的 Drop 实现就足够了
    }
}
```

编译器会用这条错误信息提出抗议：

```text
error[E0184]: the trait `Copy` cannot be implemented for this type; 
              the type has a destructor
 --> src/lib.rs:2:17
  |
2 | #[derive(Clone, Copy)]
  |                 ^^^^ `Copy` not allowed on types with destructors
```

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/13_drop.md)
