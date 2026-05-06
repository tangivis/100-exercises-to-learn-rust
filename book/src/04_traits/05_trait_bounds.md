# 特质约束 (Trait bounds)

到目前为止，我们看到了特质 (trait) 的两种用途：

- 解锁"内建"的行为（例如运算符重载）
- 给已存在的类型添加新行为（即扩展特质 (extension trait)）

还有第三种用途：**泛型编程 (generic programming)**。

## 问题 (The problem)

到目前为止，我们所有的函数和方法都是基于**具体类型 (concrete type)** 工作的。\
处理具体类型的代码通常容易写也容易理解。但它的可重用性受限。\
比如，假设我们想写一个函数，用来判断一个整数是否为偶数。
基于具体类型，我们就得为每一个想支持的整数类型分别写一个函数：

```rust
fn is_even_i32(n: i32) -> bool {
    n % 2 == 0
}

fn is_even_i64(n: i64) -> bool {
    n % 2 == 0
}

// 等等。
```

或者，我们可以写一个扩展特质，再为每个整数类型分别实现它：

```rust
trait IsEven {
    fn is_even(&self) -> bool;
}

impl IsEven for i32 {
    fn is_even(&self) -> bool {
        self % 2 == 0
    }
}

impl IsEven for i64 {
    fn is_even(&self) -> bool {
        self % 2 == 0
    }
}

// 等等。
```

重复仍然存在。

## 泛型编程 (Generic programming)

我们可以用**泛型 (generics)** 做得更好。\
泛型让我们写出基于**类型参数 (type parameter)** 而非具体类型工作的代码：

```rust
fn print_if_even<T>(n: T)
where
    T: IsEven + Debug
{
    if n.is_even() {
        println!("{n:?} is even");
    }
}
```

`print_if_even` 是一个**泛型函数 (generic function)**。\
它不绑死在某个具体输入类型上，而是适用于任何同时满足下列条件的类型 `T`：

- 实现了 `IsEven` 特质。
- 实现了 `Debug` 特质。

这个契约通过一个**特质约束 (trait bound)** 来表达：`T: IsEven + Debug`。\
`+` 符号用来要求 `T` 实现多个特质。`T: IsEven + Debug` 等价于"`T` 同时实现了 `IsEven` **与** `Debug`"。

## 特质约束 (Trait bounds)

特质约束在 `print_if_even` 中起到什么作用？\
我们试着把它去掉来看看：

```rust
fn print_if_even<T>(n: T) {
    if n.is_even() {
        println!("{n:?} is even");
    }
}
```

这段代码无法编译：

```text
error[E0599]: no method named `is_even` found for type parameter `T` 
              in the current scope
 --> src/lib.rs:2:10
  |
1 | fn print_if_even<T>(n: T) {
  |                  - method `is_even` not found 
  |                    for this type parameter
2 |     if n.is_even() {
  |          ^^^^^^^ method not found in `T`

error[E0277]: `T` doesn't implement `Debug`
 --> src/lib.rs:3:19
  |
3 |         println!("{n:?} is even");
  |                   ^^^^^ 
  |   `T` cannot be formatted using `{:?}` because 
  |         it doesn't implement `Debug`
  |
help: consider restricting type parameter `T`
  |
1 | fn print_if_even<T: std::fmt::Debug>(n: T) {
  |                   +++++++++++++++++
```

没有特质约束，编译器不知道 `T` **能做什么**。\
它不知道 `T` 有 `is_even` 方法，也不知道怎么把 `T` 格式化用于打印。
从编译器的视角看，光秃秃的 `T` 没有任何行为。\
特质约束通过确保函数体所需的行为是存在的，来限制可用的类型集合。

## 语法：内联特质约束 (Syntax: inlining trait bounds)

上面的例子都使用了 **`where` 子句**来指定特质约束：

```rust
fn print_if_even<T>(n: T)
where
    T: IsEven + Debug
//  ^^^^^^^^^^^^^^^^^
//  这就是 `where` 子句
{
    // [...]
}
```

如果特质约束很简单，你可以直接**内联 (inline)** 在类型参数旁边：

```rust
fn print_if_even<T: IsEven + Debug>(n: T) {
    //           ^^^^^^^^^^^^^^^^^
    //           这是内联特质约束
    // [...]
}
```

## 语法：使用有意义的名字 (Syntax: meaningful names)

上面的例子里，我们使用 `T` 作为类型参数名。当函数只有一个类型参数时，这是常见的约定。\
不过没什么阻止你使用更有意义的名字：

```rust
fn print_if_even<Number: IsEven + Debug>(n: Number) {
    // [...]
}
```

事实上，当涉及多个类型参数、或是 `T` 名字本身不能体现该类型在函数中的角色时，使用更有意义的名字是**值得提倡**的。
取类型参数名时，要像取变量名或函数参数名一样最大限度地追求清晰和可读性。
不过要遵循 Rust 的命名约定：使用[符合 RFC 430 的大驼峰命名 (upper camel case) 来命名类型参数](https://rust-lang.github.io/api-guidelines/naming.html#casing-conforms-to-rfc-430-c-case)。

## 函数签名说了算 (The function signature is king)

你可能会问：我们到底为什么需要特质约束？编译器不能从函数体推断出所需的特质吗？\
它能，但不会这么做。\
这与[函数参数上的显式类型注解](../02_basic_calculator/02_variables.md#函数参数也是变量-function-arguments-are-variables)的理由是一样的：
每个函数签名都是调用方与被调用方之间的契约 (contract)，条款必须明确写出来。
这能带来更好的错误信息、更好的文档、版本之间更少的意外破坏，以及更快的编译速度。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/05_trait_bounds.md)
