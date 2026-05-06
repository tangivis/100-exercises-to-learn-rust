# 可能失败 (Fallibility)

让我们重新审视前一个练习中的 `Ticket::new` 函数：

```rust
impl Ticket {
    pub fn new(
        title: String, 
        description: String, 
        status: Status
    ) -> Ticket {
        if title.is_empty() {
            panic!("Title cannot be empty");
        }
        if title.len() > 50 {
            panic!("Title cannot be longer than 50 bytes");
        }
        if description.is_empty() {
            panic!("Description cannot be empty");
        }
        if description.len() > 500 {
            panic!("Description cannot be longer than 500 bytes");
        }

        Ticket {
            title,
            description,
            status,
        }
    }
}
```

只要其中一项检查失败，函数就 panic。
这样并不理想，因为它没有给调用方**处理错误 (handle the error)** 的机会。

是时候介绍 `Result` 类型了——Rust 用来处理错误的主要机制。

## `Result` 类型 (The `Result` type)

`Result` 类型是定义在标准库中的枚举：

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

它有两个变体：

- `Ok(T)`：表示操作成功。携带 `T`，即操作的输出。
- `Err(E)`：表示操作失败。携带 `E`，即发生的错误。

`Ok` 和 `Err` 都是泛型的，让你可以为成功和失败两种情况分别指定自己的类型。

## 没有异常 (No exceptions)

Rust 中的可恢复错误 (recoverable errors) 被**表示为值 (represented as values)**。\
它们就是某个类型的实例，被传递、被操作，跟其他任何值一样。
这与 Python、C# 等使用**异常 (exception)** 来发出错误信号的语言有显著区别。

异常会创建一条独立的控制流路径，难以推理。\
仅看一个函数的签名，你不知道它是否会抛出异常。
仅看签名，你不知道它会抛出**哪种**异常。\
你必须读文档或看实现才能弄清楚。

异常处理逻辑的局部性 (locality) 很差：抛异常的代码跟捕异常的代码相距甚远，二者之间没有直接的关联。

## 可能失败被编码到类型系统中 (Fallibility is encoded in the type system)

Rust 用 `Result` 强制你**把可能失败编码进函数签名**。\
如果一个函数可能失败（且你希望调用方有机会处理错误），它必须返回 `Result`。

```rust
// 仅看签名你就知道这个函数可能失败。
// 你也可以查看 `ParseIntError` 看会出现什么类型的失败。
fn parse_int(s: &str) -> Result<i32, ParseIntError> {
    // ...
}
```

这就是 `Result` 的最大优势：让可能失败显式化。

不过请记住 panic 是存在的。它跟其他语言的异常一样，不被类型系统跟踪。
但它们是为**不可恢复错误 (unrecoverable errors)** 准备的，应当谨慎使用。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/06_fallibility.md)
