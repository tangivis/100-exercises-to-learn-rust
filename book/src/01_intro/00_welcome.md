# 欢迎

> 🌏 **中文版说明**
> 本书正在翻译中，当前进度约 5%。访问 [100.rust-roof.top](https://100.rust-roof.top) 查看最新中文版。
> 英文原版请访问 [rust-exercises.com](https://rust-exercises.com)。
> 翻译问题请提交到 [GitHub Issues](https://github.com/tangivis/100-exercises-to-learn-rust/issues)。

欢迎来到 **"100个Rust练习"**！

本课程将通过一个个练习，教你Rust的核心概念。
你将学习Rust的语法、类型系统、标准库和生态系统。

我们不假设你有任何Rust的先验知识，但假设你至少了解另一种编程语言。
我们也不假设你有系统编程或内存管理的先验知识。这些主题将在课程中介绍。

换句话说，我们将从零开始！
你将以小而易管理的步骤逐步建立你的Rust知识。
课程结束时，你将完成约100个练习，足以让你在中小型Rust项目中感到舒适。

## 方法论

本课程基于"做中学"的原则。
它被设计为互动式和实践性的。

[Mainmatter](https://mainmatter.com/rust-consulting/) 开发了这门课程，
用于在课堂环境中授课，为期4天：每位学员按自己的节奏推进课程，
经验丰富的讲师提供指导，回答问题并根据需要深入探讨主题。
你可以在[我们的网站](https://ti.to/mainmatter/rust-from-scratch-jan-2025)上报名参加下一期辅导课程。
如果你想为你的公司组织私人课程，请[联系我们](https://mainmatter.com/contact/)。

你也可以自学这门课程，但我们建议你找一个朋友或导师在你遇到困难时帮助你。
你可以在[GitHub仓库的`solutions`分支](https://github.com/mainmatter/100-exercises-to-learn-rust/tree/solutions)
中找到所有练习的解决方案。

## 格式

你可以[在浏览器中](https://100.rust-roof.top)浏览课程中文材料，
或[下载PDF文件](https://rust-exercises.com/100-exercises-to-learn-rust.pdf)离线阅读。
如果你喜欢打印版本，可以[在亚马逊购买纸质书](https://www.amazon.com/dp/B0DJ14KQQG/)。

## 结构

在屏幕左侧，你可以看到课程分为多个章节。
每个章节介绍Rust语言的一个新概念或特性。
为了验证你的理解，每个章节都配有一个需要解决的练习。

你可以在[配套的GitHub仓库](https://github.com/mainmatter/100-exercises-to-learn-rust)中找到练习。
开始课程之前，请确保将仓库克隆到本地：

```bash
# 如果你已经在GitHub上设置了SSH密钥
git clone git@github.com:mainmatter/100-exercises-to-learn-rust.git
# 否则，使用HTTPS URL：
#   https://github.com/mainmatter/100-exercises-to-learn-rust.git
```

我们还建议你在一个分支上工作，这样你可以轻松跟踪进度，
并在需要时从主仓库拉取更新：

```bash
cd 100-exercises-to-learn-rust
git checkout -b my-solutions
```

所有练习都位于`exercises`文件夹中。
每个练习都构建为一个Rust包。
包中包含练习本身、要做什么的说明（在`src/lib.rs`中），
以及自动验证你的解决方案的测试套件。

### 工具

要学习本课程，你需要：

- [**Rust**](https://www.rust-lang.org/tools/install)。
  如果系统上已安装`rustup`，运行`rustup update`（或根据你安装Rust的方式使用其他适当的命令）以确保你运行的是最新的稳定版本。
- _（可选但推荐）_ 支持Rust自动完成的IDE。
  我们推荐以下之一：
  - [RustRover](https://www.jetbrains.com/rust/)；
  - [Visual Studio Code](https://code.visualstudio.com) 配合 [`rust-analyzer`](https://marketplace.visualstudio.com/items?itemName=matklad.rust-analyzer) 扩展。

### Workshop runner, `wr`

为了验证你的解决方案，我们还提供了一个工具来指导你完成课程：`wr` CLI，即"workshop runner"的缩写。
按照[其网站](https://mainmatter.github.io/rust-workshop-runner/)上的说明安装`wr`。

安装`wr`后，打开一个新终端并导航到仓库的顶级文件夹。
运行`wr`命令开始课程：

```bash
wr
```

`wr`将验证当前练习的解决方案。
在解决当前章节的练习之前，不要进入下一章节。

> 我们建议在课程进展过程中将你的解决方案提交到Git，
> 这样你可以轻松跟踪进度，并在需要时从已知点"重新开始"。

享受课程！

## 作者

本课程由 [Luca Palmieri](https://www.lpalmieri.com/) 编写，
他是 [Mainmatter](https://mainmatter.com/rust-consulting/) 的首席工程咨询顾问。
Luca 自2018年以来一直在使用Rust，最初在TrueLayer，然后在AWS。
Luca 是 ["Zero to Production in Rust"](https://zero2prod.com) 的作者，
这是学习如何在Rust中构建后端应用程序的首选资源。
他还是各种开源Rust项目的作者和维护者，包括
[`cargo-chef`](https://github.com/LukeMathWalker/cargo-chef)、
[Pavex](https://pavex.dev) 和 [`wiremock`](https://github.com/LukeMathWalker/wiremock-rs)。

---

> 📝 **翻译说明**
> 中文翻译：[tom]
> 本翻译版本遵循 CC BY-NC 4.0 许可证，仅供非商业用途使用。
> 原作者和所有链接均已保留。
