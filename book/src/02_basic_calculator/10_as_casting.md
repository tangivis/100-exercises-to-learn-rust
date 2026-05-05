# 类型转换，第一部分 (Conversions, pt. 1)

我们一遍又一遍地强调过，Rust 不会对整数 (integer) 执行
隐式的类型转换 (implicit type conversion)。\
那么如何执行_显式_的类型转换呢？

## `as`

你可以使用 `as` 运算符在不同的整数类型 (integer type) 之间进行转换。\
`as` 转换是**绝对成功的 (infallible)**。

例如：

```rust
let a: u32 = 10;

// 把 `a` 转换为 `u64` 类型
let b = a as u64;

// 如果编译器能正确推断出目标类型，
// 你也可以使用 `_` 作为目标类型。
// 例如：
let c: u64 = a as _;
```

这个转换的语义符合你的预期：所有的 `u32` 值都是合法的 `u64`
值。

### 截断 (Truncation)

如果反向转换，事情就有趣了：

```rust
// 一个无法装入 `u8` 的
// 数字
let a: u16 = 255 + 1;
let b = a as u8;
```

这个程序运行时不会有任何问题，因为 `as` 转换是绝对成功的 (infallible)。
但 `b` 的值是什么？
当从一个较大的整数类型 (integer type) 转到较小的整数类型时，Rust 编译器 (compiler) 会执行
**截断 (truncation)**。

为了理解发生了什么，让我们先看看 `256u16` 在内存中
是如何以位序列表示的：

```text
 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0
|               |               |
+---------------+---------------+
   前 8 位          后 8 位
```

转换为 `u8` 时，Rust 编译器 (compiler) 会保留 `u16`
内存表示的后 8 位：

```text
 0 0 0 0 0 0 0 0 
|               |
+---------------+
     后 8 位
```

因此 `256 as u8` 等于 `0`。在大多数情况下，这……并不理想。\
事实上，如果 Rust 编译器 (compiler) 看到你试图
对一个会导致截断 (truncation) 的字面量值进行类型转换，它会主动尝试阻止你：

```text
error: literal out of range for `i8`
  |
4 |     let a = 255 as i8;
  |             ^^^
  |
  = note: the literal `255` does not fit into the type `i8` 
          whose range is `-128..=127`
  = help: consider using the type `u8` instead
  = note: `#[deny(overflowing_literals)]` on by default
```

### 建议 (Recommendation)

作为一条经验法则，使用 `as` 转换时要相当小心。\
_只_在从较小类型转换到较大类型时使用它。
要从较大整数类型转换到较小整数类型，请依赖
我们将在课程后面探讨的[_可失败_转换机制 (fallible conversion machinery)](../05_ticket_v2/13_try_from.md)。

### 局限性 (Limitations)

行为出人意料并不是 `as` 转换的唯一缺点。
它的适用范围也相当有限：你只能对原始类型 (primitive type) 和少数其他特殊情况
依赖 `as` 转换。\
处理复合类型 (composite type) 时，你必须依赖
不同的转换机制（[可失败的](../05_ticket_v2/13_try_from.md)
和[绝对成功的](../04_traits/09_from.md)），我们将在后面探讨。

## 进一步阅读

- 查看 [Rust 官方参考手册](https://doc.rust-lang.org/reference/expressions/operator-expr.html#numeric-cast)
  以了解 `as` 转换在每种源/目标类型组合下的精确行为，
  以及允许的转换的详尽列表。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/02_basic_calculator/10_as_casting.md)
