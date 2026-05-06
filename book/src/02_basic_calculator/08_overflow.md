# 溢出 (Overflow)

阶乘 (factorial) 的增长相当快。\
例如，20 的阶乘是 2,432,902,008,176,640,000。这已经比 32 位整数 (integer) 的最大值 2,147,483,647 还要大了。

当一个算术运算 (arithmetic operation) 的结果大于给定整数类型 (integer type) 的最大值时，我们称之为**整数溢出 (integer overflow)**。

整数溢出 (integer overflow) 是一个问题，因为它违背了算术运算的契约 (contract)。\
对两个给定类型的整数进行算术运算的结果应该是同一类型的另一个整数。
但_数学上正确的结果_无法装入该整数类型！

> 如果结果小于给定整数类型的最小值，我们将这种情况称为**整数下溢 (integer
> underflow)**。\
> 为了简洁，本节后续我们只讨论整数溢出 (integer overflow)，但请记住，
> 我们所说的一切同样适用于整数下溢 (integer underflow)。
>
> 你在["变量"章节](02_variables.md)中编写的 `speed` 函数对某些输入组合就会发生下溢 (underflow)。
> 例如，如果 `end` 小于 `start`，`end - start` 会让 `u32` 类型下溢，因为结果本应是负数，
> 但 `u32` 无法表示负数。

## 没有自动提升 (No automatic promotion)

一种可能的方法是自动将结果提升到更大的整数类型 (integer type)。
例如，如果你在两个 `u8` 整数相加，结果是 256（即 `u8::MAX + 1`），Rust 可以选择将结果解释为
`u16`，这是下一个能容纳 256 的整数类型。

但正如我们之前讨论过的，Rust 在类型转换 (type conversion) 上相当严格。自动整数提升 (automatic integer promotion)
并不是 Rust 解决整数溢出问题的方案。

## 替代方案 (Alternatives)

既然排除了自动提升，那么发生整数溢出 (integer overflow) 时我们能做什么？\
归结起来有两种不同的方法：

- 拒绝该操作
- 给出一个能装入预期整数类型 (integer type) 的"合理"结果

### 拒绝该操作

这是最保守的方法：当发生整数溢出 (integer overflow) 时，我们停止程序。\
这是通过恐慌 (panic) 来完成的，我们已经在[“恐慌”章节](04_panics.md)中见过这个机制。

### 给出"合理"的结果

当一个算术运算 (arithmetic operation) 的结果大于给定整数类型 (integer type) 的最大值时，
你可以选择**回绕 (wrap around)**。\
如果你把给定整数类型 (integer type) 的所有可能值想象成一个圆圈，回绕 (wrap around) 的意思就是当你
到达最大值时，会从最小值重新开始。

例如，如果你对 1 和 255（即 `u8::MAX`）执行**回绕加法 (wrapping addition)**，结果是 0（即 `u8::MIN`）。
对于有符号整数 (signed integer)，原理也是一样。例如，对 127（即 `i8::MAX`）加 1 进行回绕 (wrap around)
会得到 -128（即 `i8::MIN`）。

## `overflow-checks`

Rust 让你（开发者）来选择整数溢出 (integer overflow) 发生时使用哪种方法。
这种行为由 `overflow-checks` 配置项 (profile setting) 控制。

如果 `overflow-checks` 设置为 `true`，Rust 会在整数运算溢出时**在运行时恐慌 (panic at runtime)**。
如果 `overflow-checks` 设置为 `false`，Rust 会在整数运算溢出时**回绕 (wrap around)**。

你可能想知道——什么是配置项 (profile setting)？让我们来看看！

## 配置 (Profiles)

[**配置 (profile)**](https://doc.rust-lang.org/cargo/reference/profiles.html) 是一组配置选项，可以
用来定制 Rust 代码的编译方式。

Cargo 提供了 4 个内置配置 (profile)：`dev`、`release`、`test` 和 `bench`。\
每次你运行 `cargo build`、`cargo run` 或 `cargo test` 时都会使用 `dev` 配置 (profile)。它面向本地
开发，因此牺牲了运行时性能，以换取更快的编译时间和更好的调试体验。\
而 `release` 配置 (profile) 则针对运行时性能进行优化，但会带来更长的编译时间。你需要
通过 `--release` 标志显式请求——例如 `cargo build --release` 或 `cargo run --release`。
`test` 配置 (profile) 是 `cargo test` 默认使用的配置。`test` 配置继承自 `dev` 配置的设置。
`bench` 配置 (profile) 是 `cargo bench` 默认使用的配置。`bench` 配置继承自 `release` 配置。
使用 `dev` 进行迭代开发和调试，`release` 用于优化的生产构建，\
`test` 用于正确性测试，`bench` 用于性能基准测试。

> "你以 release 模式构建你的项目了吗？"在 Rust 社区里几乎成了一句梗。\
> 它指的是那些不熟悉 Rust 的开发者，在没有意识到自己没有以 release 模式构建项目之前，
> 就在社交媒体（例如 Reddit、Twitter）上抱怨 Rust 性能差。

你也可以定义自定义配置 (profile) 或定制内置的配置。

### `overflow-check`

默认情况下，`overflow-checks` 设置为：

- `dev` 配置 (profile) 为 `true`
- `release` 配置 (profile) 为 `false`

这与两个配置 (profile) 的目标一致。\
`dev` 面向本地开发，所以它会恐慌 (panic) 以便尽早暴露潜在问题。\
而 `release` 针对运行时性能进行了调优：检查溢出会拖慢程序，所以它
更倾向于回绕 (wrap around)。

同时，两个配置 (profile) 行为不同也可能导致难以察觉的 bug。\
我们的建议是在两个配置 (profile) 中都启用 `overflow-checks`：宁可崩溃，也不要静默地产生
错误结果。在大多数情况下，运行时性能的影响微乎其微；如果你正在开发性能关键的
应用，可以通过基准测试 (benchmark) 来决定是否能承受这种开销。

## 进一步阅读

- 查看 ["Myths and legends about integer overflow in Rust"](https://huonw.github.io/blog/2016/04/myths-and-legends-about-integer-overflow-in-rust/)
  以深入了解 Rust 中的整数溢出 (integer overflow)。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/02_basic_calculator/08_overflow.md)
