# `match`

你可能在想——拿到一个枚举之后，到底能**做**什么？\
最常见的操作就是对它进行 **match（匹配）**。

```rust
enum Status {
    ToDo,
    InProgress,
    Done
}

impl Status {
    fn is_done(&self) -> bool {
        match self {
            Status::Done => true,
            // `|` 运算符让你可以同时匹配多个模式 (pattern)。
            // 它读作"`Status::ToDo` 或 `Status::InProgress`"。
            Status::InProgress | Status::ToDo => false
        }
    }
}
```

`match` 语句允许你把一个 Rust 值与一系列**模式 (pattern)** 进行比较。\
你可以把它看作类型层面的 `if`：如果 `status` 是 `Done` 变体，执行第一个块；如果是 `InProgress` 或 `ToDo` 变体，执行第二个块。

## 穷尽性 (Exhaustiveness)

这里有个关键细节：`match` 是**穷尽 (exhaustive)** 的。你必须处理所有的枚举变体。\
如果你忘了处理某个变体，Rust 会**在编译期**用一条错误信息拦下你。

例如，如果我们漏掉了 `ToDo` 变体：

```rust
match self {
    Status::Done => true,
    Status::InProgress => false,
}
```

编译器会抱怨：

```text
error[E0004]: non-exhaustive patterns: `ToDo` not covered
 --> src/main.rs:5:9
  |
5 |     match status {
  |     ^^^^^^^^^^^^ pattern `ToDo` not covered
```

这是件大事！\
代码库会随时间演化——以后你可能加入新的状态，例如 `Blocked`。Rust 编译器会对每一处缺失新变体处理逻辑的 `match` 语句报错。
这就是为什么 Rust 开发者常常赞美"编译器驱动的重构 (compiler-driven refactoring)"——编译器告诉你下一步要做什么，你只需要修复它指出的问题。

## 通配 (Catch-all)

如果你不关心其中一个或多个变体，可以用 `_` 模式作为通配 (catch-all)：

```rust
match status {
    Status::Done => true,
    _ => false
}
```

`_` 模式会匹配任何之前的模式没匹配到的值。

<div class="warning">
使用通配模式时，你_失去_了编译器驱动重构带来的好处。
如果你新增一个枚举变体，编译器_不会_告诉你某处没处理它。

如果你看重正确性，避免使用通配。利用编译器去重新审视所有匹配点，决定该如何处理新的枚举变体。

</div>

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/02_match.md)
