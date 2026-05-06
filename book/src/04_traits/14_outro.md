# 总结 (Wrapping up)

本章我们覆盖了相当多不同的特质 (trait)——而且我们只触及了表面！
你可能觉得有很多东西要记，但别担心：在写 Rust 代码时你会经常碰到这些特质，很快它们就会变成你的第二天性。

## 收尾思考 (Closing thoughts)

特质很强大，但不要滥用。\
请记住下面几条指导原则：

- 不要因为一时的便利就让函数泛型化——如果它总是只用一种类型来调用。这会在你的代码库里引入间接性，让代码更难理解和维护。
- 如果你只有一个实现，就不要创建特质。这通常意味着你不需要这个特质。
- 在合理时为自己的类型实现标准特质（`Debug`、`PartialEq` 等）。
  这能让你的类型更符合习惯用法，更易于使用，并解锁标准库及生态 crate 提供的大量功能。
- 如果你需要某个第三方 crate 在它的生态中解锁的功能，就实现该 crate 的特质。
- 警惕仅仅为了在测试中使用 mock 而把代码变成泛型的做法。这种方式的可维护性代价可能很高，通常更好的做法是采用不同的测试策略。详情可以查看[测试 master class](https://github.com/mainmatter/rust-advanced-testing-workshop)，了解高保真度测试。

## 检验你的知识 (Testing your knowledge)

继续向前之前，让我们再做最后一个练习来巩固所学。
这次你只会得到很少的指引——只有练习描述和测试可供参考。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/04_traits/14_outro.md)
