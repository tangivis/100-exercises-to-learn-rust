# 字符串切片 (String slices)

前面几章你看过不少代码里使用了**字符串字面量 (string literal)**，
比如 `"To-Do"` 或 `"A ticket description"`。
它们后面总跟着一个 `.to_string()` 或 `.into()` 调用。是时候搞清楚为什么了！

## 字符串字面量 (String literals)

你通过把原始文本用双引号括起来定义一个字符串字面量：

```rust
let s = "Hello, world!";
```

`s` 的类型是 `&str`，即**对字符串切片的引用 (a reference to a string slice)**。

## 内存布局 (Memory layout)

`&str` 与 `String` 是不同的类型——它们不能互换。\
回忆我们[此前的探讨](../03_ticket_v1/09_heap.md)中 `String` 的内存布局。
如果运行：

```rust
let mut s = String::with_capacity(5);
s.push_str("Hello");
```

我们在内存里会得到这样的图景：

```text
      +---------+--------+----------+
Stack | pointer | length | capacity | 
      |  |      |   5    |    5     |
      +--|------+--------+----------+
         |
         |
         v
       +---+---+---+---+---+
Heap:  | H | e | l | l | o |
       +---+---+---+---+---+
```

如果你还记得，我们[也分析过](../03_ticket_v1/10_references_in_memory.md) `&String` 在内存里的布局：

```text
     --------------------------------------
     |                                    |         
+----v----+--------+----------+      +----|----+
| pointer | length | capacity |      | pointer |
|    |    |   5    |    5     |      |         |
+----|----+--------+----------+      +---------+
     |        s                          &s 
     |       
     v       
   +---+---+---+---+---+
   | H | e | l | l | o |
   +---+---+---+---+---+
```

`&String` 指向存储 `String` 元数据 (metadata) 的内存位置。\
顺着这个指针走，就到了堆上分配的数据。具体来说，到达字符串的第一个字节 `H`。

如果我们想要一个表示 `s` 的**子串 (substring)** 的类型呢？例如表示 `Hello` 中的 `ello`？

## 字符串切片 (String slices)

`&str` 是对字符串的一个**视图 (view)**——指向存储在别处的一段 UTF-8 字节序列的**引用 (reference)**。
例如，可以这样从 `String` 创建一个 `&str`：

```rust
let mut s = String::with_capacity(5);
s.push_str("Hello");
// 从 `String` 创建一个字符串切片引用，
// 跳过第一个字节。
let slice: &str = &s[1..];
```

在内存里，看起来是这样：

```text
                    s                              slice
      +---------+--------+----------+      +---------+--------+
Stack | pointer | length | capacity |      | pointer | length |
      |    |    |   5    |    5     |      |    |    |   4    |
      +----|----+--------+----------+      +----|----+--------+
           |        s                           |  
           |                                    |
           v                                    | 
         +---+---+---+---+---+                  |
Heap:    | H | e | l | l | o |                  |
         +---+---+---+---+---+                  |
               ^                                |
               |                                |
               +--------------------------------+
```

`slice` 在栈上存了两条信息：

- 一个指向切片首字节的指针。
- 切片的长度。

`slice` 不拥有 (own) 数据，只是指向数据：它是对 `String` 堆上数据的**引用 (reference)**。\
当 `slice` 被 drop 时，堆上的数据并不会被释放，因为它仍然由 `s` 拥有。
这就是为什么 `slice` 没有 `capacity` 字段：它不拥有数据，因此不需要知道为这块数据分配了多大空间；它只关心自己引用的部分。

## `&str` 与 `&String`

经验法则：当你需要对文本数据的引用时，用 `&str` 而不是 `&String`。\
`&str` 更灵活，在 Rust 代码里通常也被认为更符合习惯用法 (idiomatic)。

如果一个方法返回 `&String`，你就承诺：在某处存在一段堆分配的 UTF-8 文本，与你返回引用所指的那段**完全匹配**。\
如果方法返回的是 `&str`，则你拥有更多自由：你只是说"_某处_有一堆文本数据，其中某一部分是我需要的，因此返回对它的引用"。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/06_str_slice.md)
