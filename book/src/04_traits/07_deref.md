# `Deref` 特质 (`Deref` trait)

在前一个练习中你其实没做太多事，对吧？

把

```rust
impl Ticket {
    pub fn title(&self) -> &String {
        &self.title
    }
}
```

改成

```rust
impl Ticket {
    pub fn title(&self) -> &str {
        &self.title
    }
}
```

就足以让代码通过编译并通过测试。
但你脑子里应该响起一些警铃。

## 它本不该工作，但偏偏工作了 (It shouldn't work, but it does)

来梳理一下：

- `self.title` 是 `String`
- 因此 `&self.title` 是 `&String`
- （改动后）`title` 方法的输出类型是 `&str`

你会预期得到一个编译错误，对吧？类似 `Expected &String, found &str` 之类的。
然而它就是工作了。**为什么**？

## `Deref` 来救场 (`Deref` to the rescue)

`Deref` 特质是被称作[**解引用强制转换 (deref coercion)**](https://doc.rust-lang.org/std/ops/trait.Deref.html#deref-coercion) 的语言特性背后的机制。\
该特质定义在标准库的 `std::ops` 模块中：

```rust
// 我现在略微简化了定义。
// 后面我们会看到完整定义。
pub trait Deref {
    type Target;
    
    fn deref(&self) -> &Self::Target;
}
```

`type Target` 是一个**关联类型 (associated type)**。\
它是一个占位符，代表在实现该特质时必须指定的具体类型。

## 解引用强制转换 (Deref coercion)

通过为类型 `T` 实现 `Deref<Target = U>`，你就告诉编译器：`&T` 与 `&U` 在某种程度上可以互换。\
具体来说，你会得到下列行为：

- 对 `T` 的引用会被隐式转换为对 `U` 的引用（即 `&T` 变成 `&U`）
- 你可以在 `&T` 上调用所有 `U` 上接受 `&self` 作为输入的方法。

围绕解引用运算符 `*` 还有一点细节，但我们暂时用不上（如果你好奇可以看 `std` 的文档）。

## `String` 实现了 `Deref`

`String` 实现了 `Deref`，且 `Target = str`：

```rust
impl Deref for String {
    type Target = str;
    
    fn deref(&self) -> &str {
        // [...]
    }
}
```

得益于这个实现以及解引用强制转换，`&String` 在需要时会自动被转换成 `&str`。

## 不要滥用解引用强制转换 (Don't abuse deref coercion)

解引用强制转换是个强大的特性，但也容易引发困惑。\
自动类型转换会让代码更难读、更难懂。如果在 `T` 和 `U` 上都定义了同名方法，会调用哪个？

我们会在课程后面看看解引用强制转换的"最安全"使用场景：智能指针 (smart pointer)。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/07_deref.md)
