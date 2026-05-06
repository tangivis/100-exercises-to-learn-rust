# 线程 (Threads)

在我们开始写多线程代码之前，先退一步谈谈线程是什么、我们为什么要用它。

## 什么是线程？(What is a thread?)

**线程 (thread)** 是由底层操作系统管理的执行上下文。\
每个线程有自己的栈和指令指针 (instruction pointer)。

一个**进程 (process)** 可以管理多个线程。
这些线程共享同一片内存空间，意味着它们可以访问同样的数据。

线程是**逻辑 (logical)** 构造。最终，CPU 核心（**物理 (physical)** 执行单元）一次只能运行一组指令。\
由于线程数可能远多于 CPU 核心数，操作系统的**调度器 (scheduler)** 负责决定在任意时刻运行哪个线程，把 CPU 时间在它们之间划分以最大化吞吐量和响应性。

## `main`

Rust 程序启动时，运行在单个线程上——**主线程 (main thread)**。\
这个线程由操作系统创建，负责运行 `main` 函数。

```rust
use std::thread;
use std::time::Duration;

fn main() {
    loop {
        thread::sleep(Duration::from_secs(2));
        println!("Hello from the main thread!");
    }
}
```

## `std::thread`

Rust 标准库提供了 `std::thread` 模块，允许你创建和管理线程。

### `spawn`

可以用 `std::thread::spawn` 创建新线程并在其上执行代码。

例如：

```rust
use std::thread;
use std::time::Duration;

fn main() {
    let handle = thread::spawn(|| {
        loop {
            thread::sleep(Duration::from_secs(1));
            println!("Hello from a thread!");
        }
    });
    
    loop {
        thread::sleep(Duration::from_secs(2));
        println!("Hello from the main thread!");
    }
}
```

如果你在 [Rust playground](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=afedf7062298ca8f5a248bc551062eaa) 上执行这段程序，
你会看到主线程和被 spawn 出来的线程并发运行，各自独立地推进。

### 进程终止 (Process termination)

主线程结束时，整个进程也会退出。\
被 spawn 的线程会一直运行，直到它自己结束或主线程结束。

```rust
use std::thread;
use std::time::Duration;

fn main() {
    let handle = thread::spawn(|| {
        loop {
            thread::sleep(Duration::from_secs(1));
            println!("Hello from a thread!");
        }
    });

    thread::sleep(Duration::from_secs(5));
}
```

上面的例子中，你应当能看到 "Hello from a thread!" 大约被打印 5 次。\
然后主线程结束（`sleep` 调用返回时），而被 spawn 的线程也会因为整个进程退出而被终止。

### `join`

你也可以通过对 `spawn` 返回的 `JoinHandle` 调用 `join` 方法来等待 spawn 的线程结束。

```rust
use std::thread;
fn main() {
    let handle = thread::spawn(|| {
        println!("Hello from a thread!");
    });

    handle.join().unwrap();
}
```

这个例子里，主线程会等被 spawn 的线程完成后再退出。\
这就在两个线程之间引入了一种**同步 (synchronization)** 形式：你可以确保程序退出之前一定能看到 "Hello from a thread!"，因为主线程要等到被 spawn 的线程结束才会退出。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/07_threads/01_threads.md)
