# 模块 (Modules)

你刚刚定义的 `new` 方法试图在 `Ticket` 的字段值上强制施加一些**约束 (constraint)**。
但这些不变量 (invariant) 真的被强制执行了吗？是什么阻止开发者绕过 `Ticket::new` 来创建一个 `Ticket`？

要实现真正的**封装 (encapsulation)**，你需要熟悉两个新概念：**可见性 (visibility)** 和**模块 (module)**。
我们先从模块讲起。

## 什么是模块？(What is a module?)

在 Rust 中，**模块 (module)** 是一种把相关代码聚合在一起、放在一个共同命名空间（即模块名）下的方式。\
你已经见过模块的实际用法：用来验证代码正确性的单元测试 (unit test) 就定义在一个名为 `tests` 的独立模块中。

```rust
#[cfg(test)]
mod tests {
    // [...]
}
```

## 内联模块 (Inline modules)

上面的 `tests` 模块是**内联模块 (inline module)** 的例子：模块声明 (`mod tests`) 和模块内容（`{ ... }` 内的部分）紧挨在一起。

## 模块树 (Module tree)

模块可以嵌套，从而形成一棵**树**结构。\
树的根是 **crate** 本身，也就是包含所有其他模块的顶级模块。
对于库 crate (library crate)，根模块通常是 `src/lib.rs`（除非自定义了它的位置）。
根模块也叫作 **crate 根 (crate root)**。

crate 根可以有子模块，子模块还可以再有自己的子模块，依此类推。

## 外部模块与文件系统 (External modules and the filesystem)

内联模块适合放小段代码，但项目变大后，你会想把代码拆到多个文件中。在父模块里，你用 `mod` 关键字声明子模块的存在。

```rust
mod dog;
```

接下来，由 Rust 的构建工具 `cargo` 负责找到包含模块实现的文件。\
如果你的模块声明在 crate 根（例如 `src/lib.rs` 或 `src/main.rs`），`cargo` 期待文件名是以下之一：

- `src/<模块名>.rs`
- `src/<模块名>/mod.rs`

如果你的模块是另一个模块的子模块，文件应当命名为：

- `[..]/<父模块>/<模块名>.rs`
- `[..]/<父模块>/<模块名>/mod.rs`

例如，如果 `dog` 是 `animals` 的子模块，则路径为 `src/animals/dog.rs` 或 `src/animals/dog/mod.rs`。

当你用 `mod` 关键字声明新模块时，IDE 通常会帮你自动创建这些文件。

## 项目路径与 `use` 语句 (Item paths and `use` statements)

在同一模块内访问已定义的项 (item) 不需要任何特殊语法，直接用名字即可。

```rust
struct Ticket {
    // [...]
}

// 这里不需要对 `Ticket` 做任何限定
// 因为我们处于同一个模块中
fn mark_ticket_as_done(ticket: Ticket) {
    // [...]
}
```

但要访问另一个模块中的实体，情况就不一样了。\
你必须使用一个**路径 (path)** 来指向想访问的实体。

可以用多种方式组合路径：

- 从当前 crate 的根开始，例如 `crate::module_1::MyStruct`
- 从父模块开始，例如 `super::my_function`
- 从当前模块开始，例如 `sub_module_1::MyStruct`

`crate` 和 `super` 都是**关键字**。\
`crate` 指向当前 crate 的根；`super` 指向当前模块的父模块。

每次都要写完整路径会很啰嗦。
为了让你的生活更轻松，可以用 `use` 语句把实体引入作用域 (scope)。

```rust
// 把 `MyStruct` 引入作用域
use crate::module_1::module_2::MyStruct;

// 现在可以直接使用 `MyStruct`
fn a_function(s: MyStruct) {
     // [...]
}
```

### 星号导入 (Star imports)

也可以用一条 `use` 语句导入模块中的所有项。

```rust
use crate::module_1::module_2::*;
```

这种用法称为**星号导入 (star import)**。\
通常不建议使用，因为它会污染当前命名空间，让人难以分辨每个名字的来源，还可能引入命名冲突。\
不过在某些场景下它仍然有用，比如写单元测试时。你可能注意到我们大部分测试模块开头都有一句 `use super::*;`，把父模块（被测模块）中的所有项引入作用域。

## 可视化模块树 (Visualizing the module tree)

如果你很难想象项目的模块树，可以试试用 [`cargo-modules`](https://crates.io/crates/cargo-modules) 来可视化它！

具体的安装与使用方法请参考其文档。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/03_ticket_v1/03_modules.md)
