# 可空性 (Nullability)

我们对 `assigned` 方法的实现相当粗糙：对待办或已完成的工单直接 panic 并不理想。\
我们可以用 **Rust 的 `Option` 类型**做得更好。

## `Option`

`Option` 是 Rust 中表示**可空值 (nullable values)** 的类型。\
它是一个枚举，定义在 Rust 标准库中：

```rust
enum Option<T> {
    Some(T),
    None,
}
```

`Option` 编码了"值可能存在 (`Some(T)`) 或不存在 (`None`)"的概念。\
它还**强制你显式处理两种情况**。如果你处理一个可空值时忘了处理 `None` 的情形，会得到一个编译错误。\
比起其他语言中"隐式"的可空性（你可以忘记检查 `null`，进而触发运行时错误），这是个显著进步。

## `Option` 的定义 (`Option`'s definition)

`Option` 的定义使用了一个你之前没见过的 Rust 构造：**类元组变体 (tuple-like variants)**。

### 类元组变体 (Tuple-like variants)

`Option` 有两个变体：`Some(T)` 和 `None`。\
`Some` 是一个**类元组变体 (tuple-like variant)**：它持有**未命名字段 (unnamed fields)**。

类元组变体常用于只需要存储单个字段的情形，特别是像 `Option` 这样的"包装 (wrapper)"类型。

### 类元组结构体 (Tuple-like structs)

类元组并不只属于枚举——你也可以定义类元组的结构体：

```rust
struct Point(i32, i32);
```

然后可以通过位置索引访问 `Point` 实例的两个字段：

```rust
let point = Point(3, 4);
let x = point.0;
let y = point.1;
```

### 元组 (Tuples)

我们还没见过元组，却在说"类元组"，这有点奇怪！\
元组是 Rust 原始类型 (primitive type) 的另一个例子。
它们将固定数量、可能不同类型的值组合在一起：

```rust
// 两个值，相同类型
let first: (i32, i32) = (3, 4);
// 三个值，不同类型
let second: (i32, u32, u8) = (-42, 3, 8);
```

语法很简单：把值的类型用逗号分隔列在括号里。
你可以用点号加字段索引访问元组的字段：

```rust
assert_eq!(second.0, -42);
assert_eq!(second.1, 3);
assert_eq!(second.2, 8);
```

当你不想专门定义一个结构体类型时，元组是把若干个值聚在一起的便捷方式。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/05_nullability.md)
