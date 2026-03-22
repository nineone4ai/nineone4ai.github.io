---
title: "OpenSpec 概述与核心概念"
date: 2025-02-10 10:00:00 +0800
categories: [OpenSpec]
tags: ["openspec", "sdd", "spec-driven-development", "ai-coding"]
author: 戴晓峰
---

# OpenSpec 概述与核心概念

> OpenSpec 简介
> **OpenSpec** 是一个**规范驱动开发（Spec-Driven Development, SDD）**框架，专为 AI 辅助编程设计。它通过结构化的规范文档来对齐开发者和 AI 的意图，确保"先约定再构建"，解决 AI 编程中的"vibe coding"问题。

---

## 什么是 OpenSpec

### 核心理念

OpenSpec 源于一个简单的观察：

> "当你在没有明确规范的情况下提示 AI 时，你实际上是在希望它能推断出你的完整意图。"

**OpenSpec 的解决方案**：在编写代码之前，先创建明确的规范文档，让人类和 AI 就"要构建什么"达成一致。

### 设计哲学

OpenSpec 遵循以下核心原则：

```
→ 流畅而非僵化（fluid not rigid）
→ 迭代而非瀑布（iterative not waterfall）
→ 简单而非复杂（easy not complex）
→ 为遗留项目而生（built for brownfield not just greenfield）
→ 从个人项目到企业级可扩展（scalable from personal projects to enterprises）
```

### 与 Vibe Coding 的对比

| 维度         | Vibe Coding  | OpenSpec (SDD) |
| ---------- | ------------ | -------------- |
| **工作方式**   | 直接提示 AI 生成代码 | 先创建规范，再基于规范构建  |
| **意图对齐**   | 容易产生误解       | 明确的规范作为共同理解基础  |
| **可维护性**   | 容易产生技术债务     | 规范作为文档，易于理解    |
| **团队协作**   | 个人风格差异大      | 规范标准化团队工作      |
| **AI 一致性** | 输出不稳定        | 基于规范的输出更可预测    |

---

## 核心概念

### 1. 规范驱动开发（SDD）

**SDD** 是一种软件开发方法论，强调**规范（Specification）**在整个开发流程中的核心地位。

**传统开发 vs SDD**：

```
传统开发：
需求 → 设计 → 编码 → 测试 → 部署
（规范隐含在各阶段文档中）

SDD：
规范（Spec）
    ↓
编码 ← 规范验证 → 测试
    ↓
部署
（规范是唯一的真相源）
```

### 2. 单一真相源（Single Source of Truth）

OpenSpec 的核心创新是维护一个**统一的规范文档**作为系统的权威参考。

**传统方式的问题**：
```
问题：规范分散在多个文件中
├── PRD.md（产品需求）
├── API.md（接口文档）
├── ARCH.md（架构设计）
└── README.md（使用说明）

后果：
- 系统整体意图难以理解
- 功能交互问题直到实现才发现
- 验证规范与实际系统的一致性困难
```

**OpenSpec 的方式**：
```
解决方案：单一真相源规范
openspec/
└── specs/
    └── main.spec.md  ← 包含所有规范的统一文档

优势：
- 完整的系统视图
- 易于追踪变更
- 可直接与代码对比验证
```

### 3. 变更规范（Delta Specs）

OpenSpec 使用**变更规范（Delta Specs）**来管理系统的演进。

**什么是 Delta Spec**：
- 表示提议的修改
- 使用 `ADDED`、`MODIFIED`、`REMOVED` 标记变更
- 让人类和 AI 都能清楚理解变更内容

**示例**：
```markdown
## 用户认证模块

### ADDED
- 新增 JWT token 验证
- 新增刷新 token 机制

### MODIFIED
- 登录接口返回格式更新
  - 之前：{ token: string }
  - 之后：{ accessToken: string, refreshToken: string, expiresIn: number }

### REMOVED
- 移除 session-based 认证（已迁移至 JWT）
```

### 4. 双文件夹架构

OpenSpec 采用简洁的双文件夹结构：

```
openspec/
├── specs/          # 真相源 - 当前规范
│   ├── main.spec.md
│   ├── api.spec.md
│   └── ...
└── changes/        # 活跃提案和任务
    ├── change-001/
    │   ├── proposal.md
    │   ├── design.md
    │   └── tasks.md
    └── change-002/
        └── ...
```

**specs/** 目录：
- 包含系统的权威规范
- 单一真相源
- 所有变更最终合并到这里

**changes/** 目录：
- 存放活跃的变更提案
- 每个变更独立文件夹
- 包含规范更新、设计文档、实现任务

---

## OpenSpec 的核心优势

### 1. 解决"Vibe Coding"问题

**Vibe Coding 的问题**：
```
开发者："帮我添加用户认证"
AI：生成代码
开发者："不对，我要 JWT 不是 session"
AI：修改代码
开发者："还需要刷新 token"
AI：再次修改
...
（多次迭代，效率低下）
```

**OpenSpec 的解决方案**：
```
开发者：创建变更提案
AI：生成完整规范（认证方式、token 类型、刷新机制、API 设计）
开发者：审查并确认规范
AI：基于确认的规范生成代码
（一次对齐，高效实现）
```

### 2. 提升 AI 输出质量

通过明确的规范，AI 能够：
- 理解完整的上下文
- 遵循已确认的设计决策
- 生成符合规范的代码
- 减少反复修改

### 3. 为遗留项目（Brownfield）设计

OpenSpec 特别适合已有代码库的项目：
- 不需要重写整个系统
- 通过变更逐步演进
- 保持向后兼容性
- 明确的变更边界

### 4. 增强团队协作

规范文档作为团队的共同语言：
- 产品、开发、测试基于同一规范工作
- 代码审查有明确标准
- 新成员快速理解系统

---

## OpenSpec 的适用场景

### 推荐使用

| 场景          | 说明                               |
| ----------- | -------------------------------- |
| **AI 辅助编程** | 与 Claude Code、Cursor 等 AI 工具配合使用 |
| **复杂功能开发**  | 需要多步骤、多组件协作的功能                   |
| **遗留项目维护**  | 在现有代码库上增量开发                      |
| **团队协作**    | 需要标准化开发流程的团队                     |
| **长期项目**    | 需要良好文档和可维护性的项目                   |

### 不太适合

| 场景         | 原因          |
| ---------- | ----------- |
| **快速原型验证** | 初期可能过于重量级   |
| **简单脚本**   | 简单任务不需要完整规范 |
| **个人学习项目** | 学习阶段不需要严格流程 |

---

## OpenSpec vs 其他工具

### 与 PRD（产品需求文档）对比

| 对比项 | 传统 PRD | OpenSpec |
|--------|----------|----------|
| **更新频率** | 初期创建，后期很少更新 | 持续演进，与代码同步 |
| **详细程度** | 高Level，不涉及实现细节 | 包含 API、数据模型等实现细节 |
| **AI 友好** | 需要人工转换 | 结构化，AI 可直接使用 |
| **验证机制** | 无 | 可验证实现是否符合规范 |

### 与 GitHub Spec Kit 对比

| 对比项 | Spec Kit | OpenSpec |
|--------|----------|----------|
| **复杂度** | 较重，四阶段门控工作流 | 轻量，三步工作流 |
| **适用场景** | 新功能（0→1） | 遗留项目（1→n） |
| **GitHub 集成** | 深度集成 | 可选集成 |
| **灵活性** | 流程固定 | 高度可定制 |

### 与 Kiro 对比

| 对比项 | Kiro | OpenSpec |
|--------|------|----------|
| **设计理念** | IDE 集成 | 文件系统驱动 |
| **规范存储** | IDE 内部 | 项目文件夹中 |
| **可移植性** | 依赖 IDE | 任何编辑器可用 |
| **版本控制** | 部分支持 | 完全基于 Git |

---

## 核心术语表

| 术语 | 英文 | 说明 |
|------|------|------|
| **规范驱动开发** | Spec-Driven Development (SDD) | 以规范为核心的开发方法论 |
| **真相源规范** | Source of Truth Specification | 系统的权威规范文档 |
| **变更规范** | Delta Spec | 表示提议修改的规范 |
| **变更提案** | Change Proposal | 提议的功能或修改 |
| **归档** | Archive | 将完成的变更合并到真相源 |
| **快速模式** | Fast-forward | 快速创建所有变更工件 |
| **探索模式** | Explore | 研究问题，澄清需求 |

---

## 快速开始预览

OpenSpec 工作流的三个核心命令：

```bash
# 1. 提案 - 创建变更
/openspec-new-change
# AI 分析需求，生成 proposal.md

# 2. 实施 - 基于规范开发
/openspec-apply
# AI 基于规范生成代码

# 3. 归档 - 合并到真相源
/openspec-archive
# 将变更规范合并到 specs/
```

详细工作流程请参考：OpenSpec 工作流程详解

---

## 相关文档

- OpenSpec 工作流程详解 - 详细的工作流程说明
- OpenSpec 实践指南 - 实际操作指南
- OpenSpec 进阶与最佳实践 - 高级用法和建议

---

## 参考资源

- [OpenSpec GitHub](https://github.com/Fission-AI/OpenSpec)
- [OpenSpec 官网](https://openspec.dev/)
- [Oracle Agent Spec](https://github.com/oracle/agent-spec) - 另一个相关的 Agent 规范项目
- [Spec-Driven Development 介绍](https://intent-driven.dev/knowledge/openspec/)

---

*文档版本: 1.0*
*最后更新: 2025-02-10*