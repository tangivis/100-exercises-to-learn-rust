# 变体可以携带数据 (Variants can hold data)

```rust
enum Status {
    ToDo,
    InProgress,
    Done,
}
```

我们的 `Status` 枚举属于通常所说的 **C 风格枚举 (C-style enum)**。\
每个变体只是一个简单的标签，类似于命名常量。这种枚举在很多编程语言中都能见到，例如 C、C++、Java、C#、Python 等。

不过 Rust 的枚举可以走得更远。我们可以**为每个变体附加数据**。

## 变体 (Variants)

假设我们想存储正在处理某张工单的人的名字。\
只有当工单处于"进行中"时我们才会有这条信息。待办或已完成的工单不会有。
我们可以通过给 `InProgress` 变体附加一个 `String` 字段来建模：

```rust
enum Status {
    ToDo,
    InProgress {
        assigned_to: String,
    },
    Done,
}
```

`InProgress` 现在是一个**类结构体变体 (struct-like variant)**。\
事实上它的语法和我们定义结构体时一致——只是被"内联"到枚举中作为一个变体。

## 访问变体数据 (Accessing variant data)

如果我们尝试在 `Status` 实例上访问 `assigned_to`：

```rust
let status: Status = /* */;

// 这无法编译
println!("Assigned to: {}", status.assigned_to);
```

编译器会拦下我们：

```text
error[E0609]: no field `assigned_to` on type `Status`
 --> src/main.rs:5:40
  |
5 |     println!("Assigned to: {}", status.assigned_to);
  |                                        ^^^^^^^^^^^ unknown field
```

`assigned_to` 是**变体特有 (variant-specific)** 的，并非所有 `Status` 实例都有它。\
要访问 `assigned_to`，我们需要使用**模式匹配 (pattern matching)**：

```rust
match status {
    Status::InProgress { assigned_to } => {
        println!("Assigned to: {}", assigned_to);
    },
    Status::ToDo | Status::Done => {
        println!("ToDo or Done");
    }
}
```

## 绑定 (Bindings)

在匹配模式 `Status::InProgress { assigned_to }` 中，`assigned_to` 是一个**绑定 (binding)**。\
我们正在**解构 (destructure)** `Status::InProgress` 变体，并把 `assigned_to` 字段绑定到一个同名的新变量上。\
如果想要，也可以把字段绑定到一个不同的变量名：

```rust
match status {
    Status::InProgress { assigned_to: person } => {
        println!("Assigned to: {}", person);
    },
    Status::ToDo | Status::Done => {
        println!("ToDo or Done");
    }
}
```

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/03_variants_with_data.md)
