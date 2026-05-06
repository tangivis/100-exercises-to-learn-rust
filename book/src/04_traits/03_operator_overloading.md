# 运算符重载 (Operator overloading)

现在我们对特质 (trait) 有了基本的理解，让我们回到**运算符重载 (operator overloading)**。
运算符重载是为 `+`、`-`、`*`、`/`、`==`、`!=` 等运算符定义自定义行为的能力。

## 运算符就是特质 (Operators are traits)

在 Rust 中，运算符就是特质。\
对于每个运算符，都有一个对应的特质来定义它的行为。
通过为你的类型实现该特质，你就**解锁**了相应运算符的使用。

例如，[`PartialEq` 特质](https://doc.rust-lang.org/std/cmp/trait.PartialEq.html)定义了 `==` 和 `!=` 运算符的行为：

```rust
// `PartialEq` 特质的定义，来自 Rust 标准库
//（这里*稍微*简化了一下，暂时只看核心部分）
pub trait PartialEq {
    // 必须实现的方法
    //
    // `Self` 是 Rust 关键字，代表
    // "正在实现该特质的那个类型"
    fn eq(&self, other: &Self) -> bool;

    // 提供的方法（已带默认实现）
    fn ne(&self, other: &Self) -> bool { ... }
}
```

当你写 `x == y` 时，编译器会查找 `x` 和 `y` 类型的 `PartialEq` 实现，并把 `x == y` 替换为 `x.eq(y)`。这是语法糖 (syntactic sugar)！

主要运算符与特质的对应关系如下：

| 运算符                   | 特质                                                                    |
| ------------------------ | ----------------------------------------------------------------------- |
| `+`                      | [`Add`](https://doc.rust-lang.org/std/ops/trait.Add.html)               |
| `-`                      | [`Sub`](https://doc.rust-lang.org/std/ops/trait.Sub.html)               |
| `*`                      | [`Mul`](https://doc.rust-lang.org/std/ops/trait.Mul.html)               |
| `/`                      | [`Div`](https://doc.rust-lang.org/std/ops/trait.Div.html)               |
| `%`                      | [`Rem`](https://doc.rust-lang.org/std/ops/trait.Rem.html)               |
| `==` 与 `!=`             | [`PartialEq`](https://doc.rust-lang.org/std/cmp/trait.PartialEq.html)   |
| `<`、`>`、`<=`、`>=`     | [`PartialOrd`](https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html) |

算术运算符位于 [`std::ops`](https://doc.rust-lang.org/std/ops/index.html) 模块下，
比较运算符位于 [`std::cmp`](https://doc.rust-lang.org/std/cmp/index.html) 模块下。

## 默认实现 (Default implementations)

`PartialEq::ne` 上的注释说 "`ne` 是一个 provided method"。\
意思是 `PartialEq` 在特质定义中为 `ne` 提供了**默认实现 (default implementation)**——也就是上面定义里被省略的 `{ ... }` 代码块。\
把那段省略的代码块展开后，它看起来是这样：

```rust
pub trait PartialEq {
    fn eq(&self, other: &Self) -> bool;

    fn ne(&self, other: &Self) -> bool {
        !self.eq(other)
    }
}
```

跟你预期的一样：`ne` 就是 `eq` 的取反。\
既然提供了默认实现，当你为自己的类型实现 `PartialEq` 时就可以省略 `ne`，只实现 `eq` 就够了：

```rust
struct WrappingU8 {
    inner: u8,
}

impl PartialEq for WrappingU8 {
    fn eq(&self, other: &WrappingU8) -> bool {
        self.inner == other.inner
    }
    
    // 这里没有 `ne` 实现
}
```

不过你也不是非得用默认实现。
实现特质时你可以选择覆盖它：

```rust
struct MyType;

impl PartialEq for MyType {
    fn eq(&self, other: &MyType) -> bool {
        // 自定义实现
    }

    fn ne(&self, other: &MyType) -> bool {
        // 自定义实现
    }
}
```

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/03_operator_overloading.md)
