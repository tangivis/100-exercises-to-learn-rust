# 错误枚举 (Error enums)

你在前一个练习里的解决方案可能感觉有点别扭：基于字符串去 `match` 不太理想！\
某位同事重做了 `Ticket::new` 返回的错误信息（例如为了改善可读性），你的调用方代码立刻就崩了。

你已经知道修复这点所需的机制：枚举！

## 对错误做出反应 (Reacting to errors)

当你想让调用方根据具体发生的错误做出不同行为时，可以用枚举来表示不同的错误情况：

```rust
// 一个错误枚举，表示从字符串解析 `u32` 时
// 可能出现的不同错误情况。
enum U32ParseError {
    NotANumber,
    TooLarge,
    Negative,
}
```

通过错误枚举，你把不同错误情况编码进了类型系统——它们成为可能失败函数签名的一部分。\
这能简化调用方的错误处理：他们可以用 `match` 表达式对不同错误情况作出反应：

```rust
match s.parse_u32() {
    Ok(n) => n,
    Err(U32ParseError::Negative) => 0,
    Err(U32ParseError::TooLarge) => u32::MAX,
    Err(U32ParseError::NotANumber) => {
        panic!("Not a number: {}", s);
    }
}
```

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/08_error_enums.md)
