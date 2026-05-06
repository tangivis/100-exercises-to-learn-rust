# 向量 (Vectors)

数组的优点也是它的弱点：大小必须在编译期就已知。
如果你尝试创建一个大小只有运行时才能知道的数组，会得到编译错误：

```rust
let n = 10;
let numbers: [u32; n];
```

```text
error[E0435]: attempt to use a non-constant value in a constant
 --> src/main.rs:3:20
  |
2 | let n = 10;
3 | let numbers: [u32; n];
  |                    ^ non-constant value
```

数组无法满足我们的工单管理系统——我们在编译期不知道要存多少工单。
这就是 `Vec` 出场的时候。

## `Vec`

`Vec` 是标准库提供的可增长数组类型。\
你可以用 `Vec::new` 函数创建一个空数组：

```rust
let mut numbers: Vec<u32> = Vec::new();
```

然后用 `push` 方法把元素压入向量：

```rust
numbers.push(1);
numbers.push(2);
numbers.push(3);
```

新值会被加到向量末尾。\
如果你在创建时就知道值，也可以用 `vec!` 宏创建一个已初始化的向量：

```rust
let numbers = vec![1, 2, 3];
```

## 访问元素 (Accessing elements)

访问元素的语法跟数组相同：

```rust
let numbers = vec![1, 2, 3];
let first = numbers[0];
let second = numbers[1];
let third = numbers[2];
```

索引必须是 `usize` 类型。\
也可以用 `get` 方法，返回 `Option<&T>`：

```rust
let numbers = vec![1, 2, 3];
assert_eq!(numbers.get(0), Some(&1));
// 越界时返回 `None`，
// 而不是 panic。
assert_eq!(numbers.get(3), None);
```

访问会做边界检查，跟数组的元素访问一样，复杂度是 O(1)。

## 内存布局 (Memory layout)

`Vec` 是堆分配 (heap-allocated) 的数据结构。\
你创建 `Vec` 时，它在堆上分配内存来存储元素。

如果你运行下面的代码：

```rust
let mut numbers = Vec::with_capacity(3);
numbers.push(1);
numbers.push(2);
```

会得到这样的内存布局：

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

`Vec` 跟踪三件事：

- 指向你在堆上预留区域的**指针 (pointer)**。
- 向量的**长度 (length)**，即向量中有多少元素。
- 向量的**容量 (capacity)**，即在堆上预留的空间能装多少元素。

这种布局看起来应当很眼熟：和 `String` 完全一样！\
这不是巧合：`String` 在底层就是定义为字节向量 `Vec<u8>`：

```rust
pub struct String {
    vec: Vec<u8>,
}
```

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/06_ticket_management/02_vec.md)
