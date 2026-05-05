# 按情况处理的行为 (Case-by-case behavior)

`overflow-checks` 是一个粗粒度的工具：它是一个全局设置，会影响整个程序。\
然而经常会有这样的情况：你希望根据上下文以不同方式处理整数溢出 (integer overflow)——有时
回绕 (wrapping) 才是正确的选择，有时恐慌 (panic) 反而更合适。

## `wrapping_` 方法

你可以通过使用 `wrapping_` 方法[^method] 来逐操作地选择回绕算术 (wrapping arithmetic)。\
例如，你可以使用 `wrapping_add` 对两个整数进行回绕加法：

```rust
let x = 255u8;
let y = 1u8;
let sum = x.wrapping_add(y);
assert_eq!(sum, 0);
```

## `saturating_` 方法

或者，你可以通过使用 `saturating_` 方法选择**饱和算术 (saturating arithmetic)**。\
饱和算术 (saturating arithmetic) 不会回绕 (wrap around)，而是会返回该整数类型的最大值或最小值。
例如：

```rust
let x = 255u8;
let y = 1u8;
let sum = x.saturating_add(y);
assert_eq!(sum, 255);
```

由于 `255 + 1` 是 `256`，比 `u8::MAX` 大，所以结果是 `u8::MAX`（255）。\
下溢 (underflow) 时则相反：`0 - 1` 是 `-1`，比 `u8::MIN` 小，所以结果是 `u8::MIN`（0）。

你无法通过 `overflow-checks` 配置项 (profile setting) 来获得饱和算术 (saturating arithmetic)——
你必须在执行算术运算时显式选择使用它。

[^method]: 你可以把方法 (method) 看作是"附加"在特定类型上的函数。
我们将在下一章中介绍方法 (method)（以及如何定义它们）。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/02_basic_calculator/09_saturating.md)
