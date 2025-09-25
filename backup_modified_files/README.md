# 100个Rust练习 - 中文版

> 通过解决100个练习来学习Rust，一次一个练习！

你听说过Rust，但从未有机会尝试？
这门课程就是为你准备的！

你将通过解决100个练习来学习Rust。\n你将从对Rust一无所知到能够开始编写自己的程序，一次一个练习。

> [!NOTE]
> 本课程由 [Mainmatter](https://mainmatter.com/rust-consulting/) 编写。\
> 这是我们 [Rust培训课程组合](https://mainmatter.com/services/workshops/rust/) 中的一门课程。\
> 如果你需要Rust咨询或培训，请查看我们的[着陆页](https://mainmatter.com/rust-consulting/)！

## 翻译说明

本项目是 [100-exercises-to-learn-rust](https://github.com/mainmatter/100-exercises-to-learn-rust) 的中文翻译版本。

- **原作者**: Mainmatter GmbH (https://mainmatter.com)
- **原项目许可**: CC BY-NC 4.0 International
- **中文翻译**: [tom]
- **翻译版本许可**: CC BY-NC 4.0 (非商业用途)

### 翻译规范

- 使用 Rust 社区公认的中文术语
- **翻译时 Rust 专业术语要在中文后用括号标注英文原文**
- 重要术语首次出现时，在中文后用括号标注英文原文

## 开始使用

访问 [rust-exercises.com](https://rust-exercises.com) 并按照那里的说明开始学习本课程。

## 环境要求

- **Rust** (按照[这里](https://www.rust-lang.org/tools/install)的说明安装)。\
  如果你的系统上已经安装了 `rustup`，运行 `rustup update`（或根据你在系统上安装Rust的方式运行其他适当的命令）
  以确保你运行的是最新的稳定版本。
- _(可选但推荐)_ 一个支持Rust自动补全的IDE。
  我们推荐以下之一：
  - [RustRover](https://www.jetbrains.com/rust/)；
  - [Visual Studio Code](https://code.visualstudio.com) 配合
    [`rust-analyzer`](https://marketplace.visualstudio.com/items?itemName=matklad.rust-analyzer) 扩展。

## 解决方案

你可以在仓库的 [`solutions` 分支](https://github.com/mainmatter/100-exercises-to-learn-rust/tree/solutions) 中找到练习的解决方案。

## 翻译进度

### 翻译状态说明
- ✅ 已完成
- 🚧 进行中
- ⏸️ 待翻译
- 📝 需要审校

### 主要文档
- [x] ✅ book/src/SUMMARY.md

### 第1章：基础 (Introduction)
- [x] ✅ 01_intro/00_welcome.md
- [x] ✅ 01_intro/01_syntax.md

### 第2章：基础计算器 (Basic Calculator)
- [x] ✅ 02_basic_calculator/00_intro.md
- [x] ✅ 02_basic_calculator/01_integers.md
- [x] ✅ 02_basic_calculator/02_variables.md
- [x] ✅ 02_basic_calculator/03_if_else.md
- [x] ✅ 02_basic_calculator/04_panics.md
- [x] ✅ 02_basic_calculator/05_factorial.md
- [ ] ⏸️ 02_basic_calculator/06_while.md
- [ ] ⏸️ 02_basic_calculator/07_for.md
- [ ] ⏸️ 02_basic_calculator/08_overflow.md
- [ ] ⏸️ 02_basic_calculator/09_saturating.md
- [ ] ⏸️ 02_basic_calculator/10_as_casting.md

### 第3章：工单 v1 (Ticket v1)
- [ ] ⏸️ 03_ticket_v1/00_intro.md
- [ ] ⏸️ 03_ticket_v1/01_struct.md
- [ ] ⏸️ 03_ticket_v1/02_validation.md
- [ ] ⏸️ 03_ticket_v1/03_modules.md
- [ ] ⏸️ 03_ticket_v1/04_visibility.md
- [ ] ⏸️ 03_ticket_v1/05_encapsulation.md
- [ ] ⏸️ 03_ticket_v1/06_ownership.md
- [ ] ⏸️ 03_ticket_v1/07_setters.md
- [ ] ⏸️ 03_ticket_v1/08_stack.md
- [ ] ⏸️ 03_ticket_v1/09_heap.md
- [ ] ⏸️ 03_ticket_v1/10_references_in_memory.md
- [ ] ⏸️ 03_ticket_v1/11_destructor.md
- [ ] ⏸️ 03_ticket_v1/12_outro.md

### 第4章：特质 (Traits)
- [ ] ⏸️ 04_traits/00_intro.md
- [ ] ⏸️ 04_traits/01_trait.md
- [ ] ⏸️ 04_traits/02_orphan_rule.md
- [ ] ⏸️ 04_traits/03_operator_overloading.md
- [ ] ⏸️ 04_traits/04_derive.md
- [ ] ⏸️ 04_traits/05_trait_bounds.md
- [ ] ⏸️ 04_traits/06_str_slice.md
- [ ] ⏸️ 04_traits/07_deref.md
- [ ] ⏸️ 04_traits/08_sized.md
- [ ] ⏸️ 04_traits/09_from.md
- [ ] ⏸️ 04_traits/10_assoc_vs_generic.md
- [ ] ⏸️ 04_traits/11_clone.md
- [ ] ⏸️ 04_traits/12_copy.md
- [ ] ⏸️ 04_traits/13_drop.md
- [ ] ⏸️ 04_traits/14_outro.md

### 第5章：工单 v2 (Ticket v2)
- [ ] ⏸️ 05_ticket_v2/00_intro.md
- [ ] ⏸️ 05_ticket_v2/01_enum.md
- [ ] ⏸️ 05_ticket_v2/02_match.md
- [ ] ⏸️ 05_ticket_v2/03_variants_with_data.md
- [ ] ⏸️ 05_ticket_v2/04_if_let.md
- [ ] ⏸️ 05_ticket_v2/05_nullability.md
- [ ] ⏸️ 05_ticket_v2/06_fallibility.md
- [ ] ⏸️ 05_ticket_v2/07_unwrap.md
- [ ] ⏸️ 05_ticket_v2/08_error_enums.md
- [ ] ⏸️ 05_ticket_v2/09_error_trait.md
- [ ] ⏸️ 05_ticket_v2/10_packages.md
- [ ] ⏸️ 05_ticket_v2/11_dependencies.md
- [ ] ⏸️ 05_ticket_v2/12_thiserror.md
- [ ] ⏸️ 05_ticket_v2/13_try_from.md
- [ ] ⏸️ 05_ticket_v2/14_source.md
- [ ] ⏸️ 05_ticket_v2/15_outro.md

### 第6章：工单管理 (Ticket Management)
- [ ] ⏸️ 06_ticket_management/00_intro.md
- [ ] ⏸️ 06_ticket_management/01_arrays.md
- [ ] ⏸️ 06_ticket_management/02_vec.md
- [ ] ⏸️ 06_ticket_management/03_resizing.md
- [ ] ⏸️ 06_ticket_management/04_iterators.md
- [ ] ⏸️ 06_ticket_management/05_iter.md
- [ ] ⏸️ 06_ticket_management/06_lifetimes.md
- [ ] ⏸️ 06_ticket_management/07_combinators.md
- [ ] ⏸️ 06_ticket_management/08_impl_trait.md
- [ ] ⏸️ 06_ticket_management/09_impl_trait_2.md
- [ ] ⏸️ 06_ticket_management/10_slices.md
- [ ] ⏸️ 06_ticket_management/11_mutable_slices.md
- [ ] ⏸️ 06_ticket_management/12_two_states.md
- [ ] ⏸️ 06_ticket_management/13_index.md
- [ ] ⏸️ 06_ticket_management/14_index_mut.md
- [ ] ⏸️ 06_ticket_management/15_hashmap.md
- [ ] ⏸️ 06_ticket_management/16_btreemap.md

### 第7章：线程 (Threads)
- [ ] ⏸️ 07_threads/00_intro.md
- [ ] ⏸️ 07_threads/01_threads.md
- [ ] ⏸️ 07_threads/02_static.md
- [ ] ⏸️ 07_threads/03_leak.md
- [ ] ⏸️ 07_threads/04_scoped_threads.md
- [ ] ⏸️ 07_threads/05_channels.md
- [ ] ⏸️ 07_threads/06_interior_mutability.md
- [ ] ⏸️ 07_threads/07_ack.md
- [ ] ⏸️ 07_threads/08_client.md
- [ ] ⏸️ 07_threads/09_bounded.md
- [ ] ⏸️ 07_threads/10_patch.md
- [ ] ⏸️ 07_threads/11_locks.md
- [ ] ⏸️ 07_threads/12_rw_lock.md
- [ ] ⏸️ 07_threads/13_without_channels.md
- [ ] ⏸️ 07_threads/14_sync.md

### 第8章：Futures (异步编程)
- [ ] ⏸️ 08_futures/00_intro.md
- [ ] ⏸️ 08_futures/01_async_fn.md
- [ ] ⏸️ 08_futures/02_spawn.md
- [ ] ⏸️ 08_futures/03_runtime.md
- [ ] ⏸️ 08_futures/04_future.md
- [ ] ⏸️ 08_futures/05_blocking.md
- [ ] ⏸️ 08_futures/06_async_aware_primitives.md
- [ ] ⏸️ 08_futures/07_cancellation.md
- [ ] ⏸️ 08_futures/08_outro.md

### 进阶内容 (Going Further)
- [ ] ⏸️ going_further.md

## 许可证

版权所有 © 2024- Mainmatter GmbH (https://mainmatter.com)，根据
[Creative Commons Attribution-NonCommercial 4.0 International license](https://creativecommons.org/licenses/by-nc/4.0/) 发布。
