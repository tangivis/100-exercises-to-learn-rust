# 析构函数 (Destructors)

引入堆 (heap) 时，我们提到过：你分配的内存需要由你自己负责释放。\
引入借用检查器 (borrow-checker) 时，我们又说在 Rust 里你很少需要直接管理内存。

这两条乍一看像是矛盾的。
让我们通过引入**作用域 (scope)** 和**析构函数 (destructor)** 来看看它们如何能合得拢。

## 作用域 (Scopes)

变量的**作用域 (scope)** 是 Rust 代码中该变量有效（即**存活 (alive)**）的区域。

变量的作用域从它声明开始。
作用域结束于以下任意一种情况：

1. 变量声明所在的代码块（即 `{}` 之间的代码）结束
   ```rust
   fn main() {
      // `x` 这里还不在作用域中
      let y = "Hello".to_string();
      let x = "World".to_string(); // <-- x 的作用域从这里开始……
      let h = "!".to_string(); //   |
   } //  <-------------- ……到这里结束
   ```
2. 变量的所有权被转移给了别人（例如某个函数或另一个变量）
   ```rust
   fn compute(t: String) {
      // 做点事情 [...]
   }

   fn main() {
       let s = "Hello".to_string(); // <-- s 的作用域从这里开始……
                   //                    | 
       compute(s); // <------------------- ……到这里结束
                   //   因为 `s` 被移动 (moved) 到了 `compute` 中
   }
   ```

## 析构函数 (Destructors)

当一个值的所有者离开作用域时，Rust 会调用它的**析构函数 (destructor)**。\
析构函数会尝试清理该值所使用的资源——尤其是它分配过的内存。

你可以通过把值传给 `std::mem::drop` 来手动调用它的析构函数。\
这就是为什么你常听到 Rust 开发者说"那个值已经被 **drop（丢弃）了**"——表示该值离开了作用域、其析构函数已被调用。

### 可视化 drop 的位置 (Visualizing drop points)

我们可以通过插入显式的 `drop` 调用来"明示"编译器替我们做了什么。回到前面的例子：

```rust
fn main() {
   let y = "Hello".to_string();
   let x = "World".to_string();
   let h = "!".to_string();
}
```

它等价于：

```rust
fn main() {
   let y = "Hello".to_string();
   let x = "World".to_string();
   let h = "!".to_string();
   // 变量按声明的反向顺序被丢弃 (drop)
   drop(h);
   drop(x);
   drop(y);
}
```

再看第二个例子，`s` 的所有权被转移给了 `compute`：

```rust
fn compute(s: String) {
   // 做点事情 [...]
}

fn main() {
   let s = "Hello".to_string();
   compute(s);
}
```

它等价于：

```rust
fn compute(t: String) {
    // 做点事情 [...]
    drop(t); // <-- 假设 `t` 在此之前没有被 drop 或被移动 (move)，
             //     编译器会在这里——它离开作用域时——调用 `drop`
}

fn main() {
    let s = "Hello".to_string();
    compute(s);
}
```

注意区别：尽管 `compute` 调用之后 `s` 在 `main` 中已经无效，但 `main` 里并没有 `drop(s)`。
当你把一个值的所有权转移给一个函数时，你也**把清理它的责任一并转移过去了**。

这能确保某个值的析构函数**最多[^leak]被调用一次**，从设计上避免了 [双重释放 bug (double free bugs)](https://owasp.org/www-community/vulnerabilities/Doubly_freeing_memory)。

### 释放后再使用 (Use after drop)

如果你试图在一个值被 drop 后还使用它，会发生什么？

```rust
let x = "Hello".to_string();
drop(x);
println!("{}", x);
```

如果你尝试编译这段代码，会得到一个错误：

```rust
error[E0382]: use of moved value: `x`
 --> src/main.rs:4:20
  |
3 |     drop(x);
  |          - value moved here
4 |     println!("{}", x);
  |                    ^ value used here after move
```

`drop` **消费 (consumes)** 了它被调用的那个值，意味着调用之后该值就不再有效。\
因此编译器会阻止你使用它，从而避免了 [释放后使用 bug (use-after-free bugs)](https://owasp.org/www-community/vulnerabilities/Using_freed_memory)。

### 丢弃引用 (Dropping references)

如果变量保存的是一个引用呢？\
例如：

```rust
let x = 42i32;
let y = &x;
drop(y);
```

当你调用 `drop(y)` 时……什么也不会发生。\
如果你真的尝试编译这段代码，会得到一条警告：

```text
warning: calls to `std::mem::drop` with a reference 
         instead of an owned value does nothing
 --> src/main.rs:4:5
  |
4 |     drop(y);
  |     ^^^^^-^
  |          |
  |          argument has type `&i32`
  |
```

这回到我们前面说的：我们只想让析构函数被调用一次。\
你可以同时持有多个指向同一个值的引用——如果其中一个引用离开作用域时就调用值的析构函数，那其他引用怎么办？
它们将指向一个不再有效的内存位置：所谓的[**悬垂指针 (dangling pointer)**](https://en.wikipedia.org/wiki/Dangling_pointer)，
是 [**释放后使用 bug (use-after-free bug)**](https://owasp.org/www-community/vulnerabilities/Using_freed_memory) 的近亲。
Rust 的所有权系统从设计上排除了这一类 bug。

[^leak]: Rust 不保证析构函数一定会运行。例如，如果你显式选择[泄漏内存 (leak memory)](../07_threads/03_leak.md)，
它们就不会运行。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/03_ticket_v1/11_destructor.md)
