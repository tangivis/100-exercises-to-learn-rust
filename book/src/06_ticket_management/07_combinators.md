# 组合子 (Combinators)

迭代器能做的远不止 `for` 循环！\
查看 `Iterator` 特质的文档，你会发现一个**庞大**的方法集合，可用于以各种方式变换、过滤、组合迭代器。

我们说说最常见的几个：

- `map`：对迭代器的每个元素应用一个函数。
- `filter`：只保留满足某谓词 (predicate) 的元素。
- `filter_map`：一步完成 `filter` 和 `map`。
- `cloned`：把对引用的迭代器转换为值的迭代器，对每个元素进行克隆 (clone)。
- `enumerate`：返回一个产生 `(index, value)` 对的新迭代器。
- `skip`：跳过迭代器的前 `n` 个元素。
- `take`：在 `n` 个元素后停止迭代器。
- `chain`：把两个迭代器合为一个。

这些方法被称作**组合子 (combinator)**。\
通常**链式 (chained)** 调用，从而以简洁可读的方式构造复杂变换：

```rust
let numbers = vec![1, 2, 3, 4, 5];
// 偶数平方和
let outcome: u32 = numbers.iter()
    .filter(|&n| n % 2 == 0)
    .map(|&n| n * n)
    .sum();
```

## 闭包 (Closures)

上面的 `filter` 和 `map` 方法在做什么？\
它们以**闭包 (closure)** 作为参数。

闭包是**匿名函数 (anonymous function)**，即不通过我们熟悉的 `fn` 语法定义的函数。\
它们用 `|args| body` 语法定义，`args` 是参数，`body` 是函数体。
`body` 可以是一段代码块或单个表达式。
例如：

```rust
// 一个匿名函数，把参数加 1
let add_one = |x| x + 1;
// 也可以用代码块写：
let add_one = |x| { x + 1 };
```

闭包可以接收多个参数：

```rust
let add = |x, y| x + y;
let sum = add(1, 2);
```

它们也能从环境中捕获 (capture) 变量：

```rust
let x = 42;
let add_x = |y| x + y;
let sum = add_x(1);
```

如有需要，可以指定参数和/或返回类型：

```rust
// 只标注输入类型
let add_one = |x: i32| x + 1;
// 也可以使用 `fn` 语法标注输入和输出
let add_one: fn(i32) -> i32 = |x| x + 1;
```

## `collect`

用组合子变换完一个迭代器之后，要做什么？\
你要么用 `for` 循环遍历变换后的值，要么把它们收集到一个集合里。

后者是用 `collect` 方法完成。\
`collect` 消费迭代器，把它的元素收集到你选择的集合里。

例如，可以把偶数的平方收集到 `Vec` 中：

```rust
let numbers = vec![1, 2, 3, 4, 5];
let squares_of_evens: Vec<u32> = numbers.iter()
    .filter(|&n| n % 2 == 0)
    .map(|&n| n * n)
    .collect();
```

`collect` 对其**返回类型 (return type)** 是泛型的。\
所以你通常需要给出类型提示来帮编译器推断正确类型。
上面的例子中，我们把 `squares_of_evens` 的类型标注为 `Vec<u32>`。
或者你也可以用**鱼叉语法 (turbofish syntax)** 来指定类型：

```rust
let squares_of_evens = numbers.iter()
    .filter(|&n| n % 2 == 0)
    .map(|&n| n * n)
    // 鱼叉语法：`<方法名>::<类型>()`
    // 之所以叫 turbofish，是因为 `::<>` 看起来像一条鱼
    .collect::<Vec<u32>>();
```

## 进一步阅读

- [`Iterator` 的文档](https://doc.rust-lang.org/std/iter/trait.Iterator.html)
  概览了 `std` 中迭代器可用的方法。
- [`itertools` crate](https://docs.rs/itertools/) 为迭代器定义了**更多**组合子。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/07_combinators.md)
