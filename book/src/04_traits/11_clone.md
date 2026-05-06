# 复制值，第一部分 (Copying values, pt. 1)

上一章我们引入了所有权 (ownership) 与借用 (borrowing)。\
我们特别强调过：

- Rust 中每个值在任意时刻只有一个所有者 (owner)。
- 当函数获取一个值的所有权（"消费 (consume) 它"）时，调用方再也不能使用那个值。

这些限制有时颇为受限。\
有时我们必须调用一个会拿走所有权的函数，但之后还想继续使用那个值。

```rust
fn consumer(s: String) { /* */ }

fn example() {
     let mut s = String::from("hello");
     consumer(s);
     s.push_str(", world!"); // error: value borrowed here after move
}
```

这就是 `Clone` 登场的时候。

## `Clone`

`Clone` 是 Rust 标准库中定义的一个特质：

```rust
pub trait Clone {
    fn clone(&self) -> Self;
}
```

它的方法 `clone` 接受 `self` 的引用，返回一个新的、由调用方**拥有 (owned)** 的同类型实例。

## 实战 (In action)

回到上面的例子，我们可以在调用 `consumer` 之前用 `clone` 创建一个新的 `String` 实例：

```rust
fn consumer(s: String) { /* */ }

fn example() {
     let mut s = String::from("hello");
     let t = s.clone();
     consumer(t);
     s.push_str(", world!"); // 不再报错
}
```

我们不把 `s` 的所有权交给 `consumer`，而是创建一个新的 `String`（通过克隆 `s`），把它交给 `consumer`。\
`s` 在 `consumer` 调用之后仍然有效、仍然可用。

## 在内存中 (In memory)

我们看看上面例子在内存中发生了什么。
当 `let mut s = String::from("hello");` 执行时，内存看起来是这样：

```text
                    s
      +---------+--------+----------+
Stack | pointer | length | capacity | 
      |  |      |   5    |    5     |
      +--|------+--------+----------+
         |
         |
         v
       +---+---+---+---+---+
Heap:  | H | e | l | l | o |
       +---+---+---+---+---+
```

当 `let t = s.clone()` 执行时，会在堆上分配一整块新区域来存储数据的副本：

```text
                    s                                    t
      +---------+--------+----------+      +---------+--------+----------+
Stack | pointer | length | capacity |      | pointer | length | capacity |
      |  |      |   5    |    5     |      |  |      |   5    |    5     |
      +--|------+--------+----------+      +--|------+--------+----------+
         |                                    |
         |                                    |
         v                                    v
       +---+---+---+---+---+                +---+---+---+---+---+
Heap:  | H | e | l | l | o |                | H | e | l | l | o |
       +---+---+---+---+---+                +---+---+---+---+---+
```

如果你来自像 Java 这样的语言，可以把 `clone` 想成创建对象深拷贝 (deep copy) 的方式。

## 实现 `Clone` (Implementing `Clone`)

要让一个类型可被 `Clone`，我们需要为它实现 `Clone` 特质。\
通常情况下，你都会通过派生 (derive) 来实现 `Clone`：

```rust
#[derive(Clone)]
struct MyType {
    // 字段
}
```

编译器会按你预期的方式为 `MyType` 实现 `Clone`：分别克隆 `MyType` 的每个字段，再用克隆出的字段构造一个新的 `MyType` 实例。\
记得你可以用 `cargo expand`（或你的 IDE）来查看派生 (derive) 宏生成的代码。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/11_clone.md)
