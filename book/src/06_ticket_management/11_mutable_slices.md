# 可变切片 (Mutable slices)

每次我们谈到切片类型（如 `str` 和 `[T]`）时，用的都是其不可变借用形式（`&str` 和 `&[T]`）。\
但切片也可以是可变的！

下面是创建可变切片的方式：

```rust
let mut numbers = vec![1, 2, 3];
let slice: &mut [i32] = &mut numbers;
```

然后你可以修改切片中的元素：

```rust
slice[0] = 42;
```

这会把 `Vec` 的第一个元素改成 `42`。

## 局限性 (Limitations)

谈不可变借用时，建议很明确：优先用切片引用而非对所有权类型的引用（例如 `&[T]` 优于 `&Vec<T>`）。\
对可变借用来说**不是**这样。

考虑下面这个场景：

```rust
let mut numbers = Vec::with_capacity(2);
let mut slice: &mut [i32] = &mut numbers;
slice.push(1);
```

这无法编译！\
`push` 是 `Vec` 上的方法，而不是切片上的方法。这是更普遍的一条原则的体现：Rust 不允许你向切片添加或移除元素，你只能修改/替换已经存在的元素。

在这一点上，`&mut Vec` 或 `&mut String` 严格强于 `&mut [T]` 或 `&mut str`。\
依据你需要执行的操作选最合适的类型。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/11_mutable_slices.md)
