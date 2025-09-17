# 类型 (Types)，第一部分

在["语法"章节](../01_intro/01_syntax.md)中，`compute` 的输入参数类型为 `u32`。\
让我们来详细解释一下这_意味着_什么。

## 基本类型 (Primitive types)

`u32` 是 Rust 的**基本类型 (primitive types)** 之一。基本类型是语言中最基础的构建块。
它们内置于语言本身——也就是说，它们不是用其他类型来定义的。

你可以组合这些基本类型来创建更复杂的类型。我们很快就会看到如何做到这一点。

## 整数 (Integers)

`u32` 特别地，是一个**无符号 32 位整数 (unsigned 32-bit integer)**。

整数 (integer) 是一个可以不带小数部分书写的数字。例如 `1` 是一个整数，而 `1.2` 不是。

### 有符号 (Signed) vs. 无符号 (Unsigned)

整数可以是**有符号 (signed)** 或**无符号 (unsigned)** 的。\
无符号整数只能表示非负数（即 `0` 或更大的数）。
有符号整数可以表示正数和负数（例如 `-1`、`12` 等）。

`u32` 中的 `u` 代表**无符号 (unsigned)**。\
有符号整数的等效类型是 `i32`，其中 `i` 代表整数 (integer)，即可以是正数或负数的任何整数。

### 位宽 (Bit width)

`u32` 中的 `32` 指的是用于在内存中表示数字的**位数[^bit]**。\
位数越多，能表示的数字范围就越大。

Rust 支持多种位宽的整数：`8`、`16`、`32`、`64`、`128`。

使用 32 位，`u32` 可以表示从 `0` 到 `2^32 - 1` 的数字（也称为 [`u32::MAX`](https://doc.rust-lang.org/std/primitive.u32.html#associatedconstant.MAX)）。\
使用相同数量的位数，有符号整数（`i32`）可以表示从 `-2^31` 到 `2^31 - 1` 的数字
（即从 [`i32::MIN`](https://doc.rust-lang.org/std/primitive.i32.html#associatedconstant.MIN)
到 [`i32::MAX`](https://doc.rust-lang.org/std/primitive.i32.html#associatedconstant.MAX)）。\
`i32` 的最大值小于 `u32` 的最大值，因为有一位用于表示数字的符号。查看[二进制补码](https://en.wikipedia.org/wiki/Two%27s_complement)表示法了解更多关于有符号整数在内存中表示方式的详细信息。

### 总结

结合两个变量（有符号/无符号和位宽），我们得到以下整数类型：

| Bit width | Signed | Unsigned |
| --------- | ------ | -------- |
| 8-bit     | `i8`   | `u8`     |
| 16-bit    | `i16`  | `u16`    |
| 32-bit    | `i32`  | `u32`    |
| 64-bit    | `i64`  | `u64`    |
| 128-bit   | `i128` | `u128`   |

## 字面量 (Literals)

**字面量 (literal)** 是在源代码中表示固定值的记号。\
例如，`42` 是表示数字四十二的 Rust 字面量。

### 字面量的类型注解

但是 Rust 中的所有值都有类型，那么... `42` 的类型是什么？

Rust 编译器 (compiler) 会尝试根据字面量的使用方式来推断其类型。\
如果你不提供任何上下文，编译器会将整数字面量默认为 `i32`。\
如果你想使用不同的类型，你可以添加所需的整数类型作为后缀——例如，`2u64` 就是一个显式类型为 `u64` 的 2。

### 字面量中的下划线

你可以使用下划线 `_` 来提高大数字的可读性。\
例如，`1_000_000` 与 `1000000` 相同。

## 算术运算符 (Arithmetic operators)

Rust 支持以下整数的算术运算符[^traits]：

- `+` 加法 (addition)
- `-` 减法 (subtraction)
- `*` 乘法 (multiplication)
- `/` 除法 (division)
- `%` 取余 (remainder)

这些运算符的优先级和结合性与数学中的相同。\
你可以使用括号来覆盖默认的优先级。例如 `2 * (3 + 4)`。

> ⚠️ **警告 (Warning)**
>
> 除法运算符 `/` 在用于整数类型时执行整数除法。
> 也就是说，结果会向零截断。例如，`5 / 2` 是 `2`，而不是 `2.5`。

## 无自动类型强制转换 (No automatic type coercion)

正如我们在上一个练习中讨论的，Rust 是一种静态类型语言。\
特别是，Rust 对类型强制转换 (type coercion) 非常严格。它不会自动将值从一种类型转换为另一种类型[^coercion]，
即使转换是无损的。你必须显式地进行转换。

例如，你不能将 `u8` 值赋给类型为 `u32` 的变量，即使所有 `u8` 值都是有效的 `u32` 值：

```rust
let b: u8 = 100;
let a: u32 = b;
```

它会抛出编译错误：

```text
error[E0308]: mismatched types
  |
3 |     let a: u32 = b;
  |            ---   ^ expected `u32`, found `u8`
  |            |
  |            expected due to this
  |
```

我们将在本课程的[后面部分](../04_traits/09_from.md)看到如何在类型之间进行转换。

## 延伸阅读 (Further reading)

- [Rust 官方书籍中的整数类型章节](https://doc.rust-lang.org/book/ch03-02-data-types.html#integer-types)

[^bit]: 位 (bit) 是计算机中最小的数据单位。它只能有两个值：`0` 或 `1`。

[^traits]: Rust 不允许你定义自定义运算符，但它让你控制内置运算符的行为。
我们将在课程中[稍后讨论运算符重载](../04_traits/03_operator_overloading.md)，在我们介绍了特质 (trait) 之后。

[^coercion]: 这个规则有一些例外，主要与引用、智能指针和人体工程学有关。我们将在[稍后介绍](../04_traits/07_deref.md)。
"所有转换都是显式的"这个心理模型在现阶段会对你很有帮助。

> 原文链接：[英文原文](https://rust-exercises.com/100-exercises/02_basic_calculator/01_integers)
