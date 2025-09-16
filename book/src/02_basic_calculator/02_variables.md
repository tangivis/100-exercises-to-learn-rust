# 变量 (Variables)

在 Rust 中，你可以使用 `let` 关键字来声明**变量 (variables)**。\
例如：

```rust
let x = 42;
```

上面我们定义了一个变量 (variable) `x` 并给它赋值为 `42`。

## 类型 (Type)

Rust 中的每个变量都必须有一个类型。这个类型可以由编译器推断，也可以由开发者显式指定。

### 显式类型注解 (Explicit type annotation)

你可以通过在变量名后添加冒号 `:` 和类型来指定变量类型。例如：

```rust
// let <变量名>: <类型> = <表达式>;
let x: u32 = 42;
```

在上面的例子中，我们显式地将 `x` 的类型约束为 `u32`。

### 类型推断 (Type inference)

如果我们不指定变量的类型，编译器会根据变量的使用上下文来尝试推断它。

```rust
let x = 42;
let y: u32 = x;
```

在上面的例子中，我们没有指定 `x` 的类型。\
`x` 后来被赋值给 `y`，而 `y` 被显式地指定为 `u32` 类型。由于 Rust 不会执行自动类型强制转换，编译器推断 `x` 的类型为 `u32`——与 `y` 相同的类型，这是唯一能让程序无错误编译的类型。

### 推断的局限性 (Inference limitations)

编译器有时需要根据变量的使用情况来帮助推断正确的变量类型。\
在这些情况下，你会得到一个编译错误，编译器会要求你提供一个显式的类型提示来解决歧义。

## 函数参数也是变量 (Function arguments are variables)

并非所有的英雄都穿披风，也并非所有的变量都用 `let` 声明。\
函数参数也是变量！

```rust
fn add_one(x: u32) -> u32 {
    x + 1
}
```

在上面的例子中，`x` 是一个 `u32` 类型的变量。\
`x` 与用 `let` 声明的变量之间唯一的区别是，函数参数**必须**显式声明其类型。编译器不会为你推断它。\
这个约束让 Rust 编译器（还有我们人类！）能够在不查看函数实现的情况下理解函数的签名。这对编译速度是一个巨大的提升[^speed]！

## 初始化 (Initialization)

你不需要在声明变量时就初始化它。\
例如

```rust
let x: u32;
```

是一个有效的变量声明。\
然而，你必须在使用变量之前初始化它。如果你没有这么做，编译器会抛出错误：

```rust
let x: u32;
let y = x + 1;
```

会抛出一个编译错误：

```text
error[E0381]: used binding `x` isn't initialized
 --> src/main.rs:3:9
  |
2 | let x: u32;
  |     - binding declared here but left uninitialized
3 | let y = x + 1;
  |         ^ `x` used here but it isn't initialized
  |
help: consider assigning a value
  |
2 | let x: u32 = 0;
  |            +++
```

[^speed]: 当涉及到编译速度时，Rust 编译器需要所有能得到的帮助。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/tree/main/book/src/02_basic_calculator/02_variables.md)