# 泛型与关联类型 (Generics and associated types)

让我们重新审视前面学过的两个特质 `From` 和 `Deref` 的定义：

```rust
pub trait From<T> {
    fn from(value: T) -> Self;
}

pub trait Deref {
    type Target;
    
    fn deref(&self) -> &Self::Target;
}
```

它们都包含类型参数。\
对 `From` 来说是泛型参数 (generic parameter) `T`。\
对 `Deref` 来说是关联类型 (associated type) `Target`。

它们有什么区别？什么时候用哪个？

## 至多一个实现 (At most one implementation)

由于解引用强制转换 (deref coercion) 的工作机制，给定类型只能有一个"目标 (target)"类型。例如 `String` 只能解引用到 `str`。
这是为了避免歧义：如果你能为同一个类型多次实现 `Deref`，那当你调用 `&self` 方法时，编译器该选哪个 `Target` 类型？

这就是 `Deref` 使用关联类型 `Target` 的原因。\
关联类型由**特质实现**唯一确定。
既然 `Deref` 不能为同一类型实现多次，那对每个类型也就只能指定一个 `Target`，不会出现歧义。

## 泛型特质 (Generic traits)

另一方面，你可以为同一类型多次实现 `From`，**只要输入类型 `T` 不同**。
例如，可以为 `WrappingU32` 同时使用 `u32` 和 `u16` 作为输入类型来实现 `From`：

```rust
impl From<u32> for WrappingU32 {
    fn from(value: u32) -> Self {
        WrappingU32 { inner: value }
    }
}

impl From<u16> for WrappingU32 {
    fn from(value: u16) -> Self {
        WrappingU32 { inner: value.into() }
    }
}
```

这样行得通，是因为 `From<u16>` 和 `From<u32>` 被视为**不同的特质**。\
不存在歧义：编译器可以根据被转换值的类型来确定使用哪一个实现。

## 案例分析：`Add` (Case study: `Add`)

作为收尾的例子，看看标准库中的 `Add` 特质：

```rust
pub trait Add<RHS = Self> {
    type Output;
    
    fn add(self, rhs: RHS) -> Self::Output;
}
```

它同时使用了两种机制：

- 它有一个泛型参数 `RHS`（right-hand side，右操作数），默认为 `Self`
- 它有一个关联类型 `Output`，表示加法结果的类型

### `RHS`

`RHS` 是泛型参数，目的是允许不同类型相加。\
例如，标准库中你能找到这两个实现：

```rust
impl Add<u32> for u32 {
    type Output = u32;
    
    fn add(self, rhs: u32) -> u32 {
      //                      ^^^
      // 这里也可以写成 `Self::Output`。
      // 编译器不在乎，只要你这里写的类型
      // 跟你上面赋给 `Output` 的类型匹配即可。
      // [...]
    }
}

impl Add<&u32> for u32 {
    type Output = u32;
    
    fn add(self, rhs: &u32) -> u32 {
        // [...]
    }
}
```

这让下面的代码可以编译通过：

```rust
let x = 5u32 + &5u32 + 6u32;
```

因为 `u32` 同时实现了 `Add<&u32>` _以及_ `Add<u32>`。

### `Output`

`Output` 表示加法结果的类型。

为什么我们需要 `Output`？不能直接用 `Self`（即实现 `Add` 的类型）作为输出吗？
能是能，但会限制特质的灵活性。例如标准库中你会看到这个实现：

```rust
impl Add<&u32> for &u32 {
    type Output = u32;

    fn add(self, rhs: &u32) -> u32 {
        // [...]
    }
}
```

它把特质实现在 `&u32` 上，但加法的结果是 `u32`。\
如果 `add` 必须返回 `Self`（这里就是 `&u32`），那这个实现就不可能[^flexible]。
`Output` 让 `std` 把实现者类型与返回类型解耦，从而支持这种情形。

另一方面，`Output` 不能是泛型参数。一旦操作数的类型确定，加法的输出类型就**必须**唯一确定。这就是为什么它是关联类型：对给定的"实现者 + 泛型参数"组合，`Output` 类型只有一种。

## 总结 (Conclusion)

回顾一下：

- 当类型必须由特定的特质实现唯一确定时，使用**关联类型 (associated type)**。
- 当你希望允许同一类型对该特质有多个实现（输入类型不同）时，使用**泛型参数 (generic parameter)**。

[^flexible]: 灵活性很少是免费的：因为多了 `Output`，特质定义更复杂，实现者还得思考要返回什么。只有当这种灵活性确实需要时，这种权衡才划算。设计自己的特质时请记住这一点。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/10_assoc_vs_generic.md)
