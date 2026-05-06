# 简洁的分支 (Concise branching)

你对前一个练习的解决方案大概是这样：

```rust
impl Ticket {
    pub fn assigned_to(&self) -> &str {
        match &self.status {
            Status::InProgress { assigned_to } => assigned_to,
            Status::Done | Status::ToDo => {
                panic!(
                    "Only `In-Progress` tickets can be \
                    assigned to someone"
                )
            }
        }
    }
}
```

你只关心 `Status::InProgress` 这一个变体。
真的有必要把所有其他变体都列出来匹配吗？

新构造来救场！

## `if let`

`if let` 构造允许你只匹配枚举的一个变体，而无需处理其他所有变体。

下面用 `if let` 简化 `assigned_to` 方法：

```rust
impl Ticket {
    pub fn assigned_to(&self) -> &str {
        if let Status::InProgress { assigned_to } = &self.status {
            assigned_to
        } else {
            panic!(
                "Only `In-Progress` tickets can be assigned to someone"
            );
        }
    }
}
```

## `let/else`

如果 `else` 分支注定要提前返回（panic 也算提前返回！），可以使用 `let/else` 构造：

```rust
impl Ticket {
    pub fn assigned_to(&self) -> &str {
        let Status::InProgress { assigned_to } = &self.status else {
            panic!(
                "Only `In-Progress` tickets can be assigned to someone"
            );
        };
        assigned_to
    }
}
```

它让你在不引入"右漂移 (right drift)"的情况下完成解构后的赋值，即赋值与前面代码处于同一缩进层级。

## 风格 (Style)

`if let` 和 `let/else` 都是符合 Rust 习惯用法 (idiomatic) 的构造。\
按你认为合适的方式使用它们来提升代码可读性，但别滥用：需要时 `match` 永远在那儿。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/04_if_let.md)
