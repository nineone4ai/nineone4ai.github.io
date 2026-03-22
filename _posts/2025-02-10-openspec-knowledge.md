---
title: "OpenSpec 知识库"
date: 2025-02-10 10:00:00 +0800
categories: [OpenSpec]
tags: ["openspec", "MOC", "index", "navigation"]
author: 戴晓峰
---

# OpenSpec 知识库

> 欢迎来到 OpenSpec 知识库
> 这是一个关于 OpenSpec（规范驱动开发框架）的完整知识库，涵盖概念介绍、工作流程、实践指南和最佳实践。

---

## 快速导航

### 📚 核心文档

| 文档 | 描述 | 难度 | 适合人群 |
|------|------|------|----------|
| OpenSpec 概述与核心概念 | OpenSpec 基础概念、设计理念、核心术语 | ⭐⭐ | 所有人 |
| OpenSpec 工作流程详解 | 三步工作流详细说明：提案→实施→归档 | ⭐⭐⭐ | 开发者 |
| OpenSpec 实践指南 | 实战案例和操作示例 | ⭐⭐⭐ | 实践者 |
| OpenSpec 进阶与最佳实践 | 高级用法、团队协作、性能优化 | ⭐⭐⭐⭐ | 团队负责人 |

---

## 什么是 OpenSpec

> **OpenSpec** 是一个**规范驱动开发（Spec-Driven Development, SDD）**框架，专为 AI 辅助编程设计。

### 核心问题

当你在没有明确规范的情况下提示 AI 时，你实际上是在希望它能推断出你的完整意图。这导致：
- AI 输出不稳定
- 反复修改浪费时间
- 代码质量不可控

### OpenSpec 的解决方案

**"先约定，再构建"**

```
传统 AI 编程：
需求 → AI 生成代码 → 修改 → 修改 → 修改...
    ↑___________________________|
    （反复对齐意图）

OpenSpec SDD：
需求 → 创建规范 → 确认规范 → 基于规范构建 → 完成
           ↓
    （一次对齐，高效实现）
```

### 核心优势

| 优势 | 说明 |
|------|------|
| **意图对齐** | 规范作为人类和 AI 的共同理解基础 |
| **可追溯** | 从需求到实现全程可追溯 |
| **可维护** | 规范文档帮助理解系统 |
| **团队协作** | 标准化团队工作流程 |

---

## 快速开始

### 1. 理解核心概念（5 分钟）

阅读 OpenSpec 概述与核心概念，了解：
- 什么是规范驱动开发（SDD）
- 单一真相源（Source of Truth）
- 变更规范（Delta Specs）
- 双文件夹架构

### 2. 学习基本工作流（10 分钟）

阅读 OpenSpec 工作流程详解，掌握三步工作流：

```bash
# 1. 提案 - 创建规范
/openspec-new-change "添加用户认证功能"

# 2. 实施 - 基于规范开发
/openspec-apply

# 3. 归档 - 合并到真相源
/openspec-archive
```

### 3. 动手实践（30 分钟）

阅读 OpenSpec 实践指南，完成实战案例：
- 创建你的第一个变更
- 实施并归档
- 体验完整流程

---

## 核心概念速览

### 规范驱动开发（SDD）

以规范为核心的开发方法论，强调：
- 先定义"要构建什么"
- 再基于规范实施
- 规范即文档，文档即规范

### 单一真相源

维护一个统一的规范文档作为系统的权威参考：

```
openspec/
└── specs/
    └── main.spec.md  ← 所有规范的统一入口
```

### 变更规范（Delta Spec）

使用 `ADDED`、`MODIFIED`、`REMOVED` 标记变更：

```markdown
## 用户认证模块

### ADDED
- JWT token 支持
- Token 刷新机制

### MODIFIED
- 登录接口返回格式
  - 之前：{ token: string }
  - 之后：{ accessToken: string, refreshToken: string }
```

### 双文件夹架构

```
openspec/
├── specs/          # 真相源 - 当前规范
│   ├── main.spec.md
│   └── ...
└── changes/        # 活跃提案和任务
    └── change-001/
        ├── proposal.md
        ├── design.md
        └── tasks.md
```

---

## 学习路径

### 🌱 初学者路径

刚接触 OpenSpec？按这个顺序学习：

```
1. 阅读 OpenSpec 概述与核心概念
   理解 SDD 理念和核心概念
   
2. 阅读 OpenSpec 工作流程详解
   学习三步工作流
   
3. 完成 OpenSpec 实践指南 中的案例
   动手实践，体验完整流程
   
4. 在实际项目中使用
   从简单功能开始
   
5. 阅读 OpenSpec 进阶与最佳实践
   掌握高级用法和团队协作
```

### 👨‍💻 开发者路径

已有 AI 编程经验，想提升效率：

```
1. 快速浏览 OpenSpec 概述与核心概念
   了解与 vibe coding 的区别
   
2. 重点学习 OpenSpec 工作流程详解
   掌握提案→实施→归档的完整流程
   
3. 参考 OpenSpec 实践指南 的案例
   学习如何处理实际问题
   
4. 在日常开发中使用 OpenSpec
   养成先写规范再编码的习惯
```

### 🏢 团队负责人路径

需要为团队引入 OpenSpec：

```
1. 完整阅读 OpenSpec 概述与核心概念
   理解 SDD 的价值
   
2. 阅读 OpenSpec 进阶与最佳实践
   了解团队协作规范和审查流程
   
3. 制定团队规范
   - 目录结构
   - 审查流程
   - 模板定制
   
4. 小范围试点
   选择 1-2 个开发者试用
   
5. 收集反馈并优化
   然后推广到整个团队
```

---

## 常用命令速查

### 核心命令

| 命令 | 功能 | 使用场景 |
|------|------|----------|
| `/openspec-new-change` | 创建新变更 | 开始一个新功能 |
| `/openspec-apply` | 实施变更 | 基于规范编写代码 |
| `/openspec-archive` | 归档变更 | 完成并合并到真相源 |

### 辅助命令

| 命令 | 功能 | 使用场景 |
|------|------|----------|
| `/openspec-ff-change` | 快速创建变更 | 紧急或简单功能 |
| `/openspec-continue-change` | 继续变更 | 分步生成工件 |
| `/openspec-explore` | 探索模式 | 研究问题，澄清需求 |
| `/openspec-verify` | 验证实施 | 归档前验证 |
| `/openspec-bulk-archive` | 批量归档 | 归档多个变更 |

---

## 常见问题

### Q1: OpenSpec 和传统的 PRD 有什么区别？

**A:**
- PRD 是静态文档，创建后很少更新
- OpenSpec 规范是"活的"，持续演进，与代码同步
- OpenSpec 结构化，AI 可直接使用
- OpenSpec 可验证实现是否符合规范

### Q2: 什么样的项目适合用 OpenSpec？

**A:** 特别适合：
- AI 辅助编程项目
- 需要维护长期文档的项目
- 多人协作项目
- 遗留项目增量开发

不太适合：
- 快速原型验证（初期可能太重）
- 简单脚本或工具
- 个人学习项目

### Q3: OpenSpec 会增加多少工作量？

**A:** 
- 初期：增加 10-20% 时间（写规范）
- 长期：节省 30-50% 时间（减少返工）
- 对于复杂功能，总体效率提升显著

### Q4: 可以用 OpenSpec 管理非代码项目吗？

**A:** 可以！OpenSpec 的核心是"规范驱动"，适用于任何需要明确需求和可追溯性的项目：
- 产品设计
- 流程优化
- 文档编写

### Q5: OpenSpec 和 Vibe Coding 矛盾吗？

**A:** 不矛盾，是互补关系：
- **Vibe Coding**：适合快速验证想法
- **OpenSpec**：适合确定要构建的功能

建议结合使用：
1. 用 Vibe Coding 快速原型验证
2. 验证成功后，用 OpenSpec 规范地重新实现

---

## 相关资源

### 官方资源

- [OpenSpec GitHub](https://github.com/Fission-AI/OpenSpec) - 22K+ Stars
- [OpenSpec 官网](https://openspec.dev/)

### 相关项目

- [Oracle Agent Spec](https://github.com/oracle/agent-spec) - Oracle 的 Agent 规范项目
- [Kiro](https://kiro.dev/) - IDE 集成的规范工具
- [Spec Kit](https://spec-kit.dev/) - GitHub 深度集成的规范工具

### 文章和讨论

- [Spec-Driven Development 介绍](https://intent-driven.dev/knowledge/openspec/)
- [OpenSpec vs Vibe Coding](https://jasoncochran.io/blog/openspec-ai-driven-specification-workflow)
- [Reddit r/OpenSpec](https://reddit.com/r/openspec) (假设)

---

## 文档统计

```
总文档数: 4 篇

覆盖主题:
- 基础概念（SDD、真相源、Delta Spec）
- 工作流程（提案→实施→归档）
- 实战案例（用户功能、数据库迁移、API 重构）
- 最佳实践（团队规范、性能优化、常见陷阱）

适用读者:
- AI 辅助编程开发者
- 软件架构师
- 技术团队负责人
- 对规范驱动开发感兴趣的开发者
```

---

## 更新日志

### 2025-02-10

- ✅ 创建 OpenSpec 概述与核心概念
- ✅ 创建 OpenSpec 工作流程详解
- ✅ 创建 OpenSpec 实践指南
- ✅ 创建 OpenSpec 进阶与最佳实践
- ✅ 创建 OpenSpec 知识库导航（本文档）

---

> 开始你的 SDD 之旅
> 准备好开始了吗？从 OpenSpec 概述与核心概念 开始，了解如何用规范驱动开发提升 AI 编程效率！

---

*知识库版本: 1.0*
*维护者: OpenCode*
*最后更新: 2025-02-10*