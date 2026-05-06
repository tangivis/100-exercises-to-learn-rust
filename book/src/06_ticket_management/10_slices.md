# 切片 (Slices)

回到 `Vec` 的内存布局：

```rust
let mut numbers = Vec::with_capacity(3);
numbers.push(1);
numbers.push(2);
```

```text
      +---------+--------+----------+
Stack | pointer | length | capacity | 
      |  |      |   2    |    3     |
      +--|------+--------+----------+
         |
         |
         v
       +---+---+---+
Heap:  | 1 | 2 | ? |
       +---+---+---+
```

我们已经指出 `String` 不过是伪装的 `Vec<u8>`。\
这个相似性应该让你提问："那 `Vec` 对应的 `&str` 是什么？"

## `&[T]`

`[T]` 是元素类型为 `T` 的连续序列的**切片 (slice)**。\
它最常以借用形式 `&[T]` 出现。

可以用多种方式从 `Vec` 创建切片引用：

```rust
let numbers = vec![1, 2, 3];
// 通过索引语法
let slice: &[i32] = &numbers[..];
// 通过方法
let slice: &[i32] = numbers.as_slice();
// 或针对元素的子集
let slice: &[i32] = &numbers[1..];
```

`Vec` 实现了 `Deref` 特质，`Target` 为 `[T]`，因此借助解引用强制转换 (deref coercion) 你可以直接在 `Vec` 上调用切片方法：

```rust
let numbers = vec![1, 2, 3];
// 出乎意料：`iter` 不是 `Vec` 上的方法！
// 它是 `&[T]` 上的方法，但你可以借助解引用强制转换
// 在 `Vec` 上调用它。
let sum: i32 = numbers.iter().sum();
```

### 内存布局 (Memory layout)

`&[T]` 是一个**胖指针 (fat pointer)**，跟 `&str` 一样。\
它由指向切片首元素的指针和切片长度组成。

如果你有一个含三个元素的 `Vec`：

```rust
let numbers = vec![1, 2, 3];
```

然后创建一个切片引用：

```rust
let slice: &[i32] = &numbers[1..];
```

会得到这样的内存布局：

```text
                  numbers                          slice
      +---------+--------+----------+      +---------+--------+
Stack | pointer | length | capacity |      | pointer | length |
      |    |    |   3    |    4     |      |    |    |   2    |
      +----|----+--------+----------+      +----|----+--------+
           |                                    |  
           |                                    |
           v                                    | 
         +---+---+---+---+                      |
Heap:    | 1 | 2 | 3 | ? |                      |
         +---+---+---+---+                      |
               ^                                |
               |                                |
               +--------------------------------+
```

### `&Vec<T>` 与 `&[T]` (`&Vec<T>` vs `&[T]`)

当你需要把一个对 `Vec` 的不可变引用传给函数时，优先用 `&[T]` 而非 `&Vec<T>`。\
这让函数可以接受任何切片，不一定要由 `Vec` 支撑。

例如，你可以传 `Vec` 中元素的子集。
但还不止于此——你也可以传一个**数组的切片**：

```rust
let array = [1, 2, 3];
let slice: &[i32] = &array;
```

数组切片和 `Vec` 切片是同一个类型：它们都是指向连续元素序列的胖指针。
对数组而言，指针指向的是栈而不是堆，但在使用切片时这无关紧要。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/10_slices.md)
