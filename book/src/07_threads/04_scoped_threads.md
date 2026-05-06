# 作用域线程 (Scoped threads)

我们到目前为止讨论的所有生命周期问题都有一个共同根源：
被 spawn 的线程可能比父线程更长寿。\
我们可以通过使用**作用域线程 (scoped threads)** 绕开这个问题。

```rust
let v = vec![1, 2, 3];
let midpoint = v.len() / 2;

std::thread::scope(|scope| {
    scope.spawn(|| {
        let first = &v[..midpoint];
        println!("Here's the first half of v: {first:?}");
    });
    scope.spawn(|| {
        let second = &v[midpoint..];
        println!("Here's the second half of v: {second:?}");
    });
});

println!("Here's v: {v:?}");
```

我们逐步拆解发生了什么。

## `scope`

`std::thread::scope` 函数创建一个新的**作用域 (scope)**。\
`std::thread::scope` 接受一个闭包作为输入，闭包带一个参数：`Scope` 实例。

## 作用域内的 spawn (Scoped spawns)

`Scope` 暴露了一个 `spawn` 方法。\
跟 `std::thread::spawn` 不同，所有通过 `Scope` spawn 的线程在作用域结束时都会**自动 join**。

如果我们把前面的例子"翻译"成 `std::thread::spawn`，会是这样：

```rust
let v = vec![1, 2, 3];
let midpoint = v.len() / 2;

let handle1 = std::thread::spawn(|| {
    let first = &v[..midpoint];
    println!("Here's the first half of v: {first:?}");
});
let handle2 = std::thread::spawn(|| {
    let second = &v[midpoint..];
    println!("Here's the second half of v: {second:?}");
});

handle1.join().unwrap();
handle2.join().unwrap();

println!("Here's v: {v:?}");
```

## 从环境借用 (Borrowing from the environment)

不过翻译过来的版本不会通过编译：编译器会抱怨 `&v` 不能从被 spawn 的线程里使用，因为它的生命周期不是 `'static`。

`std::thread::scope` 没有这个问题——你可以**安全地从环境借用 (safely borrow from the environment)**。

我们的例子里，`v` 在 spawn 之前就创建了。
它要在 `scope` 返回 _之后_ 才被丢弃。同时，所有在 `scope` 内 spawn 的线程都保证在 `scope` 返回 _之前_ 完成，因此不存在悬垂引用的风险。

编译器不会抱怨！

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/04_scoped_threads.md)
