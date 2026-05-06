# 依赖 (Dependencies)

一个包可以通过在 `Cargo.toml` 文件的 `[dependencies]` 段列出其他包来依赖它们。\
最常见的依赖声明方式是提供名称和版本：

```toml
[dependencies]
thiserror = "1"
```

这会把 `thiserror` 作为依赖加入你的包，**最低**版本为 `1.0.0`。
`thiserror` 会从 [crates.io](https://crates.io) 拉取——这是 Rust 官方包注册中心。
当你运行 `cargo build` 时，`cargo` 会经过几个阶段：

- 依赖解析 (Dependency resolution)
- 下载依赖 (Downloading the dependencies)
- 编译你的项目（你的代码与所有依赖）

如果你的项目有 `Cargo.lock` 文件且 manifest 文件没有变化，依赖解析会被跳过。
锁文件 (lockfile) 在一次成功的依赖解析后由 `cargo` 自动生成：它包含项目所用所有依赖的精确版本，确保不同构建（例如 CI）之间使用一致的版本。如果你和多个开发者协作，应当把 `Cargo.lock` 文件提交到版本控制系统。

你可以用 `cargo update` 把 `Cargo.lock` 更新为所有依赖的最新（兼容）版本。

### 路径依赖 (Path dependencies)

你也可以用**路径 (path)** 指定依赖。这在你同时开发多个本地包时很有用。

```toml
[dependencies]
my-library = { path = "../my-library" }
```

路径相对于声明依赖的那个包的 `Cargo.toml` 文件。

### 其他来源 (Other sources)

更多关于依赖来源以及如何在 `Cargo.toml` 中指定它们的细节，请查看 [Cargo 文档](https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html)。

## 开发依赖 (Dev dependencies)

你也可以声明仅在开发时需要的依赖——它们只会在你执行 `cargo test` 时被拉入。\
这些依赖放在 `Cargo.toml` 的 `[dev-dependencies]` 段：

```toml
[dev-dependencies]
static_assertions = "1.1.0"
```

我们在本书中已经用了几个这类依赖来缩短测试代码。

> 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/05_ticket_v2/11_dependencies.md)
