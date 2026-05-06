# 解包 (Unwrapping)

`Ticket::new` 现在在收到无效输入时返回 `Result` 而不是 panic。\
这对调用方意味着什么？

## 失败不能被（隐式）忽略 (Failures can't be (implicitly) ignored)

与异常 (exception) 不同，Rust 的 `Result` 强制你**在调用点 (call site) 处理错误**。\
如果你调用一个返回 `Result` 的函数，Rust 不允许你隐式忽略错误情况。

```rust
fn parse_int(s: &str) -> Result<i32, ParseIntError> {
    // ...
}

// 这无法编译：我们没处理错误情况。
// 我们必须使用 `match`，或者 `Result` 提供的某个组合子 (combinator)
// 来"解包"成功值或者处理错误。
let number = parse_int("42") + 2;
```

## 你拿到了 `Result`，然后呢？(You got a `Result`. Now what?)

调用一个返回 `Result` 的函数后，你有两个关键选项：

- 操作失败时直接 panic。
  通过 `unwrap` 或 `expect` 方法实现：
  ```rust
  // 如果 `parse_int` 返回 `Err`，则 panic。
  let number = parse_int("42").unwrap();
  // `expect` 让你指定自定义的 panic 消息。
  let number = parse_int("42").expect("Failed to parse integer");
  ```
- 用 `match` 表达式解构 `Result`，显式处理错误情况。
  ```rust
  match parse_int("42") {
      Ok(number) => println!("Parsed number: {}", number),
      Err(err) => eprintln!("Error: {}", err),
  }
  ```

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/07_unwrap.md)
