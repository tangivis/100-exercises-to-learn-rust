# 100个Rust练习 - 中文翻译进度

## 翻译说明

- ✅ 已完成
- 🚧 进行中
- ⏸️ 待翻译
- 📝 需要审校

## 版权声明

- **原作者**: Mainmatter GmbH (https://mainmatter.com)
- **原项目许可**: CC BY-NC 4.0 International
- **中文翻译**: [你的名字]
- **翻译版本许可**: CC BY-NC 4.0 (非商业用途)

## 翻译进度

### 主要文档
- [ ] ⏸️ README.md
- [ ] ⏸️ book/src/SUMMARY.md

### 第1章：基础
- [ ] ⏸️ 01_intro/00_welcome.md
- [ ] ⏸️ 01_intro/01_syntax.md
- [ ] ⏸️ 01_intro/02_basic_calculator.md

### 第2章：变量
- [ ] ⏸️ 02_basic_calculator/00_intro.md
- [ ] ⏸️ 02_basic_calculator/01_integers.md
- [ ] ⏸️ 02_basic_calculator/02_variables.md

### 第3章：控制流
- [ ] ⏸️ 03_ticket_v1/00_intro.md
- [ ] ⏸️ 03_ticket_v1/01_struct.md
- [ ] ⏸️ 03_ticket_v1/02_validation.md

### 第4章：Move语义
- [ ] ⏸️ 04_traits/00_intro.md
- [ ] ⏸️ 04_traits/01_trait.md
- [ ] ⏸️ 04_traits/02_orphan_rule.md

### 第5章：引用和借用
- [ ] ⏸️ 05_ticket_v2/00_intro.md
- [ ] ⏸️ 05_ticket_v2/01_heap.md
- [ ] ⏸️ 05_ticket_v2/02_clone.md

## 翻译规范

### 术语对照表

| 英文 | 中文 | 备注 |
|------|------|------|
| ownership | 所有权 | |
| borrowing | 借用 | |
| reference | 引用 | |
| lifetime | 生命周期 | |
| trait | trait/特征 | 保留英文或使用"特征" |
| struct | 结构体 | |
| enum | 枚举 | |
| match | 匹配 | |
| pattern matching | 模式匹配 | |
| generic | 泛型 | |
| closure | 闭包 | |
| iterator | 迭代器 | |
| async/await | 异步/等待 | |
| panic | panic/恐慌 | |
| Result | Result/结果类型 | |
| Option | Option/选项类型 | |

### 翻译原则

1. **保持代码不变**: 所有代码示例保持英文，只翻译说明文字
2. **术语一致性**: 使用术语对照表保持翻译一致
3. **保留原文链接**: 所有外部链接保持不变
4. **注释翻译**: 代码中的注释可以翻译，但要保留原英文注释
5. **保持格式**: 维持原文的Markdown格式和结构

## 分支管理

- `main`: 原始英文版本（保持同步上游）
- `zh-translation`: 中文翻译主分支
- `zh-dev`: 开发分支（日常翻译工作）

## 部署信息

- **域名**: https://100.rust-roof.top
- **平台**: Cloudflare Pages
- **构建命令**: `mdbook build`
- **构建输出**: `book/book`
- **环境变量**:
  - `MDBOOK_BOOK__LANGUAGE=zh-CN`

## 贡献指南

1. Fork 本仓库
2. 创建翻译分支：`git checkout -b translate-chapter-x`
3. 完成翻译后提交PR
4. 等待审校和合并

## 联系方式

- GitHub Issues: [你的仓库issues链接]
- Email: [你的邮箱]