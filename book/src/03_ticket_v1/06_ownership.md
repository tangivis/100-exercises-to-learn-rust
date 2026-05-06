# 所有权 (Ownership)

如果你只用本课程到目前为止所学的内容来解决前一个练习，
你的访问器方法可能长这样：

```rust
impl Ticket {
    pub fn title(self) -> String {
        self.title
    }

    pub fn description(self) -> String {
        self.description
    }

    pub fn status(self) -> String {
        self.status
    }
}
```

这些方法能编译通过，并且足以让测试通过，但放到真实场景中就走不远了。
看看下面这段代码：

```rust
if ticket.status() == "To-Do" {
    // 我们还没介绍 `println!` 宏 (macro)，
    // 但目前你只需要知道它会向控制台
    // 打印（带模板的）信息
    println!("Your next task is: {}", ticket.title());
}
```

如果你尝试编译它，会得到一个错误：

```text
error[E0382]: use of moved value: `ticket`
  --> src/main.rs:30:43
   |
25 |     let ticket = Ticket::new(/* */);
   |         ------ move occurs because `ticket` has type `Ticket`, 
   |                which does not implement the `Copy` trait
26 |     if ticket.status() == "To-Do" {
   |               -------- `ticket` moved due to this method call
...
30 |         println!("Your next task is: {}", ticket.title());
   |                                           ^^^^^^ 
   |                                value used here after move
   |
note: `Ticket::status` takes ownership of the receiver `self`, 
      which moves `ticket`
  --> src/main.rs:12:23
   |
12 |         pub fn status(self) -> String {
   |                       ^^^^
```

恭喜你，这是你的第一个借用检查器 (borrow-checker) 错误！

## Rust 所有权系统的好处 (The perks of Rust's ownership system)

Rust 的所有权系统旨在确保：

- 数据在被读取时永远不会被修改
- 数据在被修改时永远不会被读取
- 数据被销毁后永远不会被访问

这些约束由**借用检查器 (borrow checker)** 强制执行，它是 Rust 编译器的一个子系统，常常成为 Rust 社区里的笑话和梗的主角。

所有权 (ownership) 是 Rust 中的关键概念，也是这门语言独一无二的原因。
所有权让 Rust 能够提供**内存安全 (memory safety) 而不牺牲性能**。
对 Rust 来说，下面这些可以同时成立：

1. 没有运行时垃圾回收器 (runtime garbage collector)
2. 作为开发者，你很少需要直接管理内存
3. 不会出现悬垂指针 (dangling pointer)、双重释放 (double free) 以及其他与内存相关的 bug

像 Python、JavaScript 和 Java 这样的语言能给你 2 和 3，但没有 1。\
像 C 或 C++ 这样的语言给你 1，但既没有 2 也没有 3。

根据你的背景不同，第 3 点可能听起来有点神秘：什么是"悬垂指针 (dangling pointer)"？什么是"双重释放 (double free)"？为什么它们危险？\
别担心：在课程的剩余部分我们会更详细地讨论这些概念。

不过现在，我们先专注于学习如何在 Rust 的所有权系统中工作。

## 所有者 (The owner)

在 Rust 中，每个值都有一个**所有者 (owner)**，所有者在编译时被静态确定。
任意时刻，每个值都只有一个所有者。

## 移动语义 (Move semantics)

所有权可以转移。

例如，如果你拥有某个值，你可以把所有权转移给另一个变量：

```rust
let a = "hello, world".to_string(); // <- `a` 是该 String 的所有者
let b = a;  // <- `b` 现在是该 String 的所有者
```

Rust 的所有权系统是融入到类型系统中的：每个函数都必须在其签名 (signature) 中声明
它打算如何与其参数交互。

到目前为止，我们所有的方法和函数都**消费 (consumed)** 了它们的参数：它们获取了参数的所有权。
例如：

```rust
impl Ticket {
    pub fn description(self) -> String {
        self.description
    }
}
```

`Ticket::description` 获取了它被调用的 `Ticket` 实例的所有权。\
这就是所谓的**移动语义 (move semantics)**：值（`self`）的所有权从调用方**移动 (moved)** 到被调用方，调用方再也不能使用它了。

这正是编译器在前面那条错误信息里所用的措辞：

```text
error[E0382]: use of moved value: `ticket`
  --> src/main.rs:30:43
   |
25 |     let ticket = Ticket::new(/* */);
   |         ------ move occurs because `ticket` has type `Ticket`, 
   |                which does not implement the `Copy` trait
26 |     if ticket.status() == "To-Do" {
   |               -------- `ticket` moved due to this method call
...
30 |         println!("Your next task is: {}", ticket.title());
   |                                           ^^^^^^ 
   |                                 value used here after move
   |
note: `Ticket::status` takes ownership of the receiver `self`, 
      which moves `ticket`
  --> src/main.rs:12:23
   |
12 |         pub fn status(self) -> String {
   |                       ^^^^
```

具体来说，调用 `ticket.status()` 时发生了如下事件序列：

- `Ticket::status` 拿走 `Ticket` 实例的所有权
- `Ticket::status` 从 `self` 中提取出 `status`，并把 `status` 的所有权转移回调用方
- `Ticket` 实例的其余部分被丢弃（`title` 和 `description`）

接着我们尝试通过 `ticket.title()` 再次使用 `ticket` 时，编译器抱怨：`ticket` 这个值已经没了，我们不再拥有它，因此不能再使用它。

要构建_有用的_访问器方法，我们需要开始使用**引用 (reference)**。

## 借用 (Borrowing)

我们希望访问器方法能读取变量的值，而不夺走它的所有权。\
否则编程就太受限了。在 Rust 中，这通过**借用 (borrowing)** 来实现。

每当你借用一个值时，你会得到对它的一个**引用 (reference)**。\
引用按照其权限被打上标签[^refine]：

- 不可变引用 (`&`)：允许你读取值，但不能修改
- 可变引用 (`&mut`)：允许你既读取也修改值

回到 Rust 所有权系统的目标：

- 数据在被读取时永远不会被修改
- 数据在被修改时永远不会被读取

为了保证这两点，Rust 必须对引用引入一些限制：

- 不能同时存在指向同一值的可变引用和不可变引用
- 不能同时存在多个指向同一值的可变引用
- 当一个值正在被借用时，所有者不能修改这个值
- 只要不存在可变引用，你想要多少不可变引用都可以

某种程度上，你可以把不可变引用看作对该值的"只读 (read-only)"锁，
而可变引用则像"读写 (read-write)"锁。

所有这些限制都由借用检查器 (borrow checker) 在编译期强制执行。

### 语法 (Syntax)

实际上你怎么借用一个值？\
通过在变量**前面**加上 `&` 或 `&mut`，你就借用了它的值。
但要注意！同样的符号（`&` 和 `&mut`）放在**类型前面**含义不同：
它们表示一个不同的类型——对原类型的引用 (reference)。

例如：

```rust
struct Configuration {
    version: u32,
    active: bool,
}

fn main() {
    let config = Configuration {
        version: 1,
        active: true,
    };
    // `b` 是对 `config` 的 `version` 字段的引用。
    // `b` 的类型是 `&u32`，因为它包含了对一个
    // `u32` 值的引用。
    // 我们用 `&` 运算符借用 `config.version`，
    // 创建了一个引用。
    // 同样的符号 (`&`)，根据上下文含义不同！
    let b: &u32 = &config.version;
    //     ^ 类型注解 (type annotation) 不是必需的，
    //       这里写出来只是为了说明发生了什么
}
```

同样的概念也适用于函数参数和返回类型：

```rust
// `f` 接受一个对 `u32` 的可变引用作为参数，
// 绑定到名字 `number`
fn f(number: &mut u32) -> &u32 {
    // [...]
}
```

## 深呼吸 (Breathe in, breathe out)

Rust 的所有权系统初看可能有点压倒性。\
但别担心：随着练习的增多，它会变成你的第二天性。\
本章剩余部分以及整个课程都会让你练个够！
我们会反复回顾每个概念，确保你对它们足够熟悉、真正理解它们的工作原理。

到本章末尾，我们会解释 Rust 的所有权系统_为什么_要这样设计。
此刻，先专注于理解_怎么用_。把每条编译器错误都当作一次学习机会！

[^refine]: 这是个不错的入门心智模型，但它没有捕捉到_完整_的图景。
我们在课程[后面](../07_threads/06_interior_mutability.md)会进一步细化对引用 (reference) 的理解。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/03_ticket_v1/06_ownership.md)
