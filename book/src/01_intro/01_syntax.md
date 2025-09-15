# 语法(Syntax)

<div class="warning">

不要跳过！\
请先完成上一部分的练习再开始这一部分。\
练习位于[课程GitHub仓库](https://github.com/mainmatter/100-exercises-to-learn-rust)中的 `exercises/01_intro/00_welcome` 目录。\
使用[`wr`](00_welcome.md#wr-the-workshop-runner)来开始课程并验证你的解决方案。

</div>

前面的任务甚至算不上一个练习，但它已经让你接触到了不少Rust的**语法(syntax)**。
我们不会涵盖前面练习中使用的Rust语法的每一个细节。
相反，我们将覆盖_恰好够用_的内容来让我们继续前进，而不会陷入细节中。\
一步一步来！

## 注释(Comments)

你可以使用 `//` 来写单行注释(single-line comments)：

```rust
// This is a single-line comment
// Followed by another single-line comment
```

## 函数(Functions)

Rust中的函数(function)使用`fn`关键字定义，后跟函数名、输入参数(input parameters)和返回类型(return type)。
函数体(function body)包含在大括号`{}`中。

在前面的练习中，你看到了`greeting`函数：

```rust
// `fn` <function_name> ( <input params> ) -> <return_type> { <body> }
fn greeting() -> &'static str {
    // TODO: fix me 👇
    "I'm ready to __!"
}
```

`greeting`函数没有输入参数，并返回一个字符串切片(string slice)的引用(`&'static str`)。

### 返回类型(Return type)

如果函数不返回任何值(即返回`()`，Rust的单元类型(unit type))，可以从签名(signature)中省略返回类型。
这就是`test_welcome`函数的情况：

```rust
fn test_welcome() {
    assert_eq!(greeting(), "I'm ready to learn Rust!");
}
```

上面的代码等价于：

```rust
// Spelling out the unit return type explicitly
//                   👇
fn test_welcome() -> () {
    assert_eq!(greeting(), "I'm ready to learn Rust!");
}
```

### 返回值(Returning values)

函数中的最后一个表达式(expression)会被隐式返回：

```rust
fn greeting() -> &'static str {
    // This is the last expression in the function
    // Therefore its value is returned by `greeting`
    "I'm ready to learn Rust!"
}
```

你也可以使用`return`关键字来提前返回一个值：

```rust
fn greeting() -> &'static str {
    // Notice the semicolon at the end of the line!
    return "I'm ready to learn Rust!";
}
```

在可能的情况下省略`return`关键字被认为是符合惯例的(idiomatic)。

### 输入参数(Input parameters)

输入参数在函数名后的圆括号`()`内声明。\
每个参数的声明格式为：参数名，后跟冒号`:`，然后是其类型。

例如，下面的`greet`函数接受一个类型为`&str`("字符串切片(string slice)")的`name`参数：

```rust
// An input parameter
//        👇
fn greet(name: &str) -> String {
    format!("Hello, {}!", name)
}
```

如果有多个输入参数，它们必须用逗号分隔。

### 类型标注(Type annotations)

既然我们已经多次提到了"类型(types)"，让我们明确说明：Rust是一种**静态类型语言(statically typed language)**。\
Rust中的每个值都有一个类型，并且该类型必须在编译时(compile-time)被编译器(compiler)所知。

类型是一种**静态分析(static analysis)**形式。\
你可以将类型看作是编译器附加到程序中每个值的**标签(tag)**。根据不同的标签，编译器可以执行不同的规则——例如，你不能将字符串加到数字上，但你可以将两个数字相加。
如果正确利用，类型可以防止整类运行时错误(runtime bugs)。
