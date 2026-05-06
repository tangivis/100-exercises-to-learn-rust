# `'static`

如果你尝试在前一个练习里从向量借用一个切片，
你大概会得到一条这样的编译错误：

```text
error[E0597]: `v` does not live long enough
   |
11 | pub fn sum(v: Vec<i32>) -> i32 {
   |            - binding `v` declared here
...
15 |     let right = &v[split_point..];
   |                  ^ borrowed value does not live long enough
16 |     let left_handle = spawn(move || left.iter().sum::<i32>());
   |                             -------------------------------- 
                     argument requires that `v` is borrowed for `'static`
19 | }
   |  - `v` dropped here while still borrowed
```

`argument requires that v is borrowed for 'static`，这是什么意思？

`'static` 生命周期是 Rust 中一个特殊的生命周期。\
它意味着该值在程序的整个运行期内都有效。

## 分离的线程 (Detached threads)

通过 `thread::spawn` 启动的线程可以**比 (outlive) 它的父线程更长寿**。\
例如：

```rust
use std::thread;

fn f() {
    thread::spawn(|| {
        thread::spawn(|| {
            loop {
                thread::sleep(std::time::Duration::from_secs(1));
                println!("Hello from the detached thread!");
            }
        });
    });
}
```

这个例子里，第一个被 spawn 的线程又 spawn 了一个子线程，子线程每秒打印一条消息。\
第一个线程随后完成并退出。当这发生时，
其子线程会**继续运行**，只要整个进程还在运行。\
用 Rust 的术语说，子线程**比 (outlived)** 它的父线程更长寿。

## `'static` 生命周期 (`'static` lifetime)

由于 spawn 的线程可能：

- 比 spawn 它的线程（父线程）更长寿
- 一直运行到程序退出

它必须不能借用任何可能在程序退出之前被丢弃的值；
违反这个约束会让我们暴露在 use-after-free bug 之下。\
这就是为什么 `std::thread::spawn` 的签名要求传给它的闭包具有 `'static` 生命周期：

```rust
pub fn spawn<F, T>(f: F) -> JoinHandle<T> 
where
    F: FnOnce() -> T + Send + 'static,
    T: Send + 'static
{
    // [..]
}
```

## `'static` 不（仅仅）关乎引用 (`'static` is not (just) about references)

Rust 中所有值都有生命周期，不只是引用。

特别地，一个拥有自己数据的类型（例如 `Vec` 或 `String`）满足 `'static` 约束：如果你拥有它，你想用多久都行，即使最初创建它的函数已经返回了。

因此你可以把 `'static` 解读为：

- 给我一个具有所有权的值
- 给我一个在程序整个运行期内都有效的引用

第一种方式正是你在前一个练习里解决问题的方式：
分配新的向量来分别持有原向量的左半段和右半段，再把它们移入 (move) 被 spawn 的线程。

## `'static` 引用 (`'static` references)

我们来谈第二种情况：在程序整个运行期内都有效的引用。

### 静态数据 (Static data)

最常见的情形是对**静态数据 (static data)** 的引用，比如字符串字面量：

```rust
let s: &'static str = "Hello world!";
```

由于字符串字面量在编译期已知，Rust 把它们存放在你的可执行文件 _内部_，
位于一个叫**只读数据段 (read-only data segment)** 的区域。
所有指向该区域的引用因此在整个程序运行期内都有效；它们满足 `'static` 契约。

## 进一步阅读

- [The data segment](https://en.wikipedia.org/wiki/Data_segment)

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/02_static.md)
