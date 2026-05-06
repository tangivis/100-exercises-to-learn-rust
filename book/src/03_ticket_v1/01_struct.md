# 结构体 (Structs)

我们需要为每个工单 (ticket) 跟踪三项信息：

- 标题 (title)
- 描述 (description)
- 状态 (status)

可以先用 [`String`](https://doc.rust-lang.org/std/string/struct.String.html) 来表示它们。`String` 是 Rust 标准库中定义的类型，用于表示 [UTF-8 编码](https://en.wikipedia.org/wiki/UTF-8) 的文本。

但我们怎么把这三项信息**组合**成一个单一实体呢？

## 定义 `struct`

`struct`（结构体）定义一个**新的 Rust 类型**。

```rust
struct Ticket {
    title: String,
    description: String,
    status: String
}
```

结构体 (struct) 跟其他编程语言中的"类 (class)"或"对象 (object)"很相似。

## 定义字段 (Defining fields)

新类型由其他类型作为**字段 (field)** 组合而成。\
每个字段必须有名字和类型，二者用冒号 `:` 分隔。如果有多个字段，则用逗号 `,` 分隔。

字段不必是同一类型，参见下面的 `Configuration` 结构体：

```rust
struct Configuration {
   version: u32,
   active: bool
}
```

## 实例化 (Instantiation)

你可以通过为每个字段指定值来创建一个结构体实例：

```rust
// 语法：<结构体名> { <字段名>: <值>, ... }
let ticket = Ticket {
    title: "Build a ticket system".into(),
    description: "A Kanban board".into(),
    status: "Open".into()
};
```

## 访问字段 (Accessing fields)

你可以使用 `.` 运算符访问结构体的字段：

```rust
// 字段访问
let x = ticket.description;
```

## 方法 (Methods)

我们可以通过定义**方法 (method)** 来给结构体附加行为。\
以 `Ticket` 结构体为例：

```rust
impl Ticket {
    fn is_open(self) -> bool {
        self.status == "Open"
    }
}

// 语法：
// impl <结构体名> {
//    fn <方法名>(<参数>) -> <返回类型> {
//        // 方法体
//    }
// }
```

方法 (method) 与函数 (function) 非常相似，但有两个关键区别：

1. 方法必须定义在 **`impl` 块**内
2. 方法可以使用 `self` 作为它的第一个参数。
   `self` 是一个关键字，代表方法被调用时所在的结构体实例。

### `self`

如果方法的第一个参数是 `self`，可以使用**方法调用语法 (method call syntax)** 来调用：

```rust
// 方法调用语法：<实例>.<方法名>(<参数>)
let is_open = ticket.is_open();
```

这与你在[上一章](../02_basic_calculator/09_saturating.md)中对 `u32` 值执行饱和算术运算时所用的调用语法是一样的。

### 静态方法 (Static methods)

如果方法不以 `self` 作为第一个参数，那它就是**静态方法 (static method)**。

```rust
struct Configuration {
    version: u32,
    active: bool
}

impl Configuration {
    // `default` 是 `Configuration` 上的静态方法
    fn default() -> Configuration {
        Configuration { version: 0, active: false }
    }
}
```

调用静态方法的唯一方式是使用**函数调用语法 (function call syntax)**：

```rust
// 函数调用语法：<结构体名>::<方法名>(<参数>)
let default_config = Configuration::default();
```

### 等价性 (Equivalence)

即使方法的第一个参数是 `self`，你依然可以用函数调用语法来调用它：

```rust
// 函数调用语法：
//   <结构体名>::<方法名>(<实例>, <参数>)
let is_open = Ticket::is_open(ticket);
```

函数调用语法非常清楚地表明 `ticket` 被当作 `self`（方法的第一个参数）使用，但显然更冗长。可以的话还是优先使用方法调用语法。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/03_ticket_v1/01_struct.md)
