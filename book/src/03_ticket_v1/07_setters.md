# 可变引用 (Mutable references)

到现在你的访问器方法应该长这样：

```rust
impl Ticket {
    pub fn title(&self) -> &String {
        &self.title
    }

    pub fn description(&self) -> &String {
        &self.description
    }

    pub fn status(&self) -> &String {
        &self.status
    }
}
```

这里那里撒上几个 `&` 就搞定了！\
我们现在有了一种访问 `Ticket` 实例字段而又不消耗它的方式。
接下来看看怎么用**setter 方法 (setter method)** 来增强我们的 `Ticket` 结构体。

## Setter

Setter 方法允许用户修改 `Ticket` 私有字段的值，同时保证不变量 (invariant) 得到尊重（也就是说，你不能把 `Ticket` 的标题设为空字符串）。

在 Rust 中实现 setter 有两种常见方式：

- 以 `self` 作为输入。
- 以 `&mut self` 作为输入。

### 以 `self` 作为输入 (Taking `self` as input)

第一种方式像这样：

```rust
impl Ticket {
    pub fn set_title(mut self, new_title: String) -> Self {
        // 验证新标题 [...]
        self.title = new_title;
        self
    }
}
```

它拿走 `self` 的所有权，修改标题，再把修改后的 `Ticket` 实例返回。\
你会这样使用它：

```rust
let ticket = Ticket::new(
    "Title".into(), 
    "Description".into(), 
    "To-Do".into()
);
let ticket = ticket.set_title("New title".into());
```

由于 `set_title` 拿走了 `self` 的所有权（即**消费 (consumes)** 它），我们需要把结果重新赋给一个变量。
上面的例子利用了**变量遮蔽 (variable shadowing)** 来重用同一个变量名：当你用一个已存在的名字声明一个新变量时，新变量会**遮蔽 (shadow)** 旧变量。这是 Rust 代码中常见的模式。

`self` 风格的 setter 在你需要一次性修改多个字段时表现得很好：你可以把多个调用串起来！

```rust
let ticket = ticket
    .set_title("New title".into())
    .set_description("New description".into())
    .set_status("In Progress".into());
```

### 以 `&mut self` 作为输入 (Taking `&mut self` as input)

第二种 setter 方式使用 `&mut self`，看起来像这样：

```rust
impl Ticket {
    pub fn set_title(&mut self, new_title: String) {
        // 验证新标题 [...]
        
        self.title = new_title;
    }
}
```

这次方法接受的是对 `self` 的可变引用，修改标题，仅此而已。
什么也不返回。

你会这样使用它：

```rust
let mut ticket = Ticket::new(
    "Title".into(),
    "Description".into(),
    "To-Do".into()
);
ticket.set_title("New title".into());

// 使用修改后的 ticket
```

所有权仍然在调用方手里，因此原来的 `ticket` 变量依旧有效。我们不需要把结果重新赋值。
不过我们要把 `ticket` 标记为 `mut`（可变），因为我们对它取了可变引用。

`&mut` 风格的 setter 有个缺点：你不能把多个调用串起来。
因为它们不返回修改后的 `Ticket` 实例，所以你不能在第一个调用的结果上再调用另一个 setter。
你必须分别调用每个 setter：

```rust
ticket.set_title("New title".into());
ticket.set_description("New description".into());
ticket.set_status("In Progress".into());
```

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/03_ticket_v1/07_setters.md)
