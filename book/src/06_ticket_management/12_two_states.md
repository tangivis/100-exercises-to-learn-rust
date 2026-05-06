# 工单 id (Ticket ids)

让我们再思考一下我们的工单管理系统。\
当前我们的工单模型是这样：

```rust
pub struct Ticket {
    pub title: TicketTitle,
    pub description: TicketDescription,
    pub status: Status
}
```

这里漏了一样东西：用来唯一标识工单的**标识符 (identifier)**。\
该标识符对每张工单都应当唯一。这点可以通过在新工单创建时自动生成来保证。

## 细化模型 (Refining the model)

id 应当存放在哪里？\
我们可以给 `Ticket` 结构体加一个新字段：

```rust
pub struct Ticket {
    pub id: TicketId,
    pub title: TicketTitle,
    pub description: TicketDescription,
    pub status: Status
}
```

但在创建工单之前我们并不知道 id，所以它不能从一开始就在那。\
那它必须是可选的：

```rust
pub struct Ticket {
    pub id: Option<TicketId>,
    pub title: TicketTitle,
    pub description: TicketDescription,
    pub status: Status
}
```

这也不理想——每次从存储中取出工单时都要处理 `None` 情况，尽管我们知道工单创建之后 id 总应当存在。

最好的方案是：用两种独立的类型表示工单的两种**状态 (state)**——`TicketDraft` 和 `Ticket`：

```rust
pub struct TicketDraft {
    pub title: TicketTitle,
    pub description: TicketDescription
}

pub struct Ticket {
    pub id: TicketId,
    pub title: TicketTitle,
    pub description: TicketDescription,
    pub status: Status
}
```

`TicketDraft` 是尚未创建的工单。它没有 id，也没有状态。\
`Ticket` 是已创建的工单。它有 id 和状态。\
由于 `TicketDraft` 和 `Ticket` 中每个字段都内嵌了自己的约束，我们不需要在两个类型间复制逻辑。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/12_two_states.md)
