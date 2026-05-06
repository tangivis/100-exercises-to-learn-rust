# `Error::source`

要完整覆盖 `Error` 特质，还有一件事需要谈：`source` 方法。

```rust
// 这次是完整定义！
pub trait Error: Debug + Display {
    fn source(&self) -> Option<&(dyn Error + 'static)> {
        None
    }
}
```

`source` 方法是用来访问**错误成因 (error cause)** 的方式（如果有的话）。\
错误经常是链式的，意思是一个错误是另一个错误的成因：你有一个高层错误（例如：无法连接到数据库），它由更低层的错误（例如：无法解析数据库主机名）引起。
`source` 方法允许你"遍历 (walk)"整条错误链，常用于在日志中捕获错误上下文。

## 实现 `source` (Implementing `source`)

`Error` 特质提供了一个默认实现，总是返回 `None`（即没有底层成因）。这就是为什么在前面练习中你不必关心 `source`。\
你可以覆盖默认实现，为你的错误类型提供成因。

```rust
use std::error::Error;

#[derive(Debug)]
struct DatabaseError {
    source: std::io::Error
}

impl std::fmt::Display for DatabaseError {
    fn fmt(&self, f: &mut std::fmt::Formatter) -> std::fmt::Result {
        write!(f, "Failed to connect to the database")
    }
}

impl std::error::Error for DatabaseError {
    fn source(&self) -> Option<&(dyn Error + 'static)> {
        Some(&self.source)
    }
}
```

这个例子里，`DatabaseError` 把 `std::io::Error` 作为它的 source 包装起来。
我们覆盖 `source` 方法，在被调用时返回这个 source。

## `&(dyn Error + 'static)`

这个 `&(dyn Error + 'static)` 类型是什么？\
我们逐个拆开：

- `dyn Error` 是一个**特质对象 (trait object)**。它是引用任何实现了 `Error` 特质的类型的方式。
- `'static` 是一个特殊的**生命周期标注 (lifetime specifier)**。
  `'static` 意味着这个引用"在我们需要它的时间内一直有效"，即整个程序运行期。

合起来：`&(dyn Error + 'static)` 是一个对实现了 `Error` 特质的特质对象的引用，且在整个程序运行期内都有效。

这两个概念现在不必太担心。我们会在后面的章节中更详细地介绍它们。

## 用 `thiserror` 实现 `source` (Implementing `source` using `thiserror`)

`thiserror` 提供了三种自动为错误类型实现 `source` 的方式：

- 名为 `source` 的字段会自动被用作错误的 source。
  ```rust
  use thiserror::Error;

  #[derive(Error, Debug)]
  pub enum MyError {
      #[error("Failed to connect to the database")]
      DatabaseError {
          source: std::io::Error
      }
  }
  ```
- 标注了 `#[source]` 属性的字段会自动被用作错误的 source。
  ```rust
  use thiserror::Error;

  #[derive(Error, Debug)]
  pub enum MyError {
      #[error("Failed to connect to the database")]
      DatabaseError {
          #[source]
          inner: std::io::Error
      }
  }
  ```
- 标注了 `#[from]` 属性的字段会自动被用作错误的 source，**并且** `thiserror` 会自动生成一个 `From` 实现，把被标注类型转换为你的错误类型。
  ```rust
  use thiserror::Error;

  #[derive(Error, Debug)]
  pub enum MyError {
      #[error("Failed to connect to the database")]
      DatabaseError {
          #[from]
          inner: std::io::Error
      }
  }
  ```

## `?` 运算符 (The `?` operator)

`?` 运算符是错误传播 (error propagation) 的简写。\
当用在返回 `Result` 的函数中时，如果 `Result` 为 `Err`，它会提前返回该错误。

例如：

```rust
use std::fs::File;

fn read_file() -> Result<String, std::io::Error> {
    let mut file = File::open("file.txt")?;
    let mut contents = String::new();
    file.read_to_string(&mut contents)?;
    Ok(contents)
}
```

等价于：

```rust
use std::fs::File;

fn read_file() -> Result<String, std::io::Error> {
    let mut file = match File::open("file.txt") {
        Ok(file) => file,
        Err(e) => {
            return Err(e);
        }
    };
    let mut contents = String::new();
    match file.read_to_string(&mut contents) {
        Ok(_) => (),
        Err(e) => {
            return Err(e);
        }
    }
    Ok(contents)
}
```

`?` 运算符可以大幅缩短你的错误处理代码。\
特别地，如果存在合适的 `From` 实现，`?` 运算符会自动把可能失败操作的错误类型转换为函数的错误类型。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/14_source.md)
