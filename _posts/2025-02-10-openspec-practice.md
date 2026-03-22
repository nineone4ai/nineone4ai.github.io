---
title: "OpenSpec 实践指南"
date: 2025-02-10 10:00:00 +0800
categories: [OpenSpec]
tags: ["openspec", "tutorial", "practice", "examples"]
author: 戴晓峰
---

# OpenSpec 实践指南

> 实践指南
> 本指南通过实际案例，演示如何使用 OpenSpec 进行规范驱动开发。从环境搭建到完整的变更流程，帮助你快速上手 OpenSpec。

---

## 环境准备

### 1. 安装 OpenSpec CLI

OpenSpec 通过 CLI 命令与 AI 助手集成。首先确保你使用的 AI 工具支持 OpenSpec。

**支持的 AI 工具：**
- Claude Code (Anthropic)
- OpenCode
- 其他支持 OpenSpec 的 AI 助手

### 2. 项目初始化

在你的项目根目录初始化 OpenSpec：

```bash
# 创建 OpenSpec 目录结构
mkdir -p openspec/specs
mkdir -p openspec/changes

# 创建初始主规范
cat > openspec/specs/main.spec.md << 'EOF'
# 系统规范

## 概述
项目名称：[你的项目名称]
技术栈：[技术栈描述]

## 模块列表
- [用户模块](user.spec.md)
- [认证模块](auth.spec.md)
- [API 模块](api.spec.md)

## 变更历史
- 初始创建
EOF

# 创建 .gitignore（可选但推荐）
cat > openspec/.gitignore << 'EOF'
# 可以添加不需要版本控制的临时文件
*.tmp
*.draft
EOF
```

### 3. 验证环境

```bash
# 检查目录结构
ls -la openspec/

# 预期输出：
# specs/     - 真相源目录
# changes/   - 变更目录
```

---

## 实战案例 1：简单的用户功能

### 场景

为一个博客系统添加"用户资料编辑"功能。

### 步骤 1：创建变更提案

```bash
# 使用 OpenSpec 命令
/openspec-new-change "添加用户资料编辑功能"
```

**与 AI 的交互**：

```markdown
AI: 我来帮你创建这个变更提案。首先，让我分析一下需求...

    基于 "添加用户资料编辑功能"，我需要了解：
    
    1. 用户可以编辑哪些字段？
    2. 是否需要头像上传？
    3. 有什么验证规则？

用户：
- 字段：昵称、简介、个人网站
- 不需要头像（后续再添加）
- 昵称必填，2-20 字符

AI: 好的，现在创建提案...

    ✓ 创建目录：openspec/changes/change-001-user-profile-edit/
    ✓ 生成 proposal.md
    ✓ 生成 design.md
    ✓ 生成 tasks.md

    请查看生成的文档，我们可以进一步细化。
```

### 生成的提案文档

**openspec/changes/change-001-user-profile-edit/proposal.md**：

```markdown
# 提案：添加用户资料编辑功能

## Why
用户需要完善个人资料，展示个性化信息。
提升社区互动性和用户体验。

## What
实现用户资料编辑功能，包括：
- 编辑昵称、简介、个人网站
- 数据验证
- API 接口

## Scope
✅ 包含：
- 资料编辑页面/组件
- API 端点
- 数据验证
- 权限检查

❌ 不包含：
- 头像上传（后续提案）
- 用户搜索功能
- 社交功能

## Success Criteria
- [ ] 用户可以成功编辑并保存资料
- [ ] 昵称验证规则正确执行
- [ ] 未登录用户无法编辑
- [ ] 只能编辑自己的资料
```

**openspec/changes/change-001-user-profile-edit/design.md**：

```markdown
# 设计：用户资料编辑

## UI 设计

### 编辑表单
┌─────────────────────────────┐
│ 编辑个人资料                    │
├─────────────────────────────┤
│ 昵称 *                        │
│ [________________]            │
│ 提示：2-20 个字符               │
├─────────────────────────────┤
│ 简介                          │
│ [                ]            │
│ [                ]            │
│ 最多 200 字                   │
├─────────────────────────────┤
│ 个人网站                      │
│ [________________]            │
│ 可选，需为有效 URL             │
├─────────────────────────────┤
│ [保存] [取消]                 │
└─────────────────────────────┘
```

```

## API 设计

### PUT /api/users/:id/profile

**Request:**
```json
{
  "nickname": "张三",
  "bio": "热爱技术的开发者",
  "website": "https://example.com"
}
```

**Response (200):**
```json
{
  "id": "user-123",
  "nickname": "张三",
  "bio": "热爱技术的开发者",
  "website": "https://example.com",
  "updatedAt": "2025-01-15T10:30:00Z"
}
```

**Error Cases:**
- 400: 验证失败（返回具体错误信息）
- 401: 未登录
- 403: 无权限（只能编辑自己的资料）

## 数据验证

| 字段 | 规则 | 错误信息 |
|------|------|----------|
| nickname | 必填, 2-20 字符 | "昵称必填，2-20 个字符" |
| bio | 可选, 最多 200 字 | "简介最多 200 字" |
| website | 可选, 有效 URL | "请输入有效的网址" |
```

**openspec/changes/change-001-user-profile-edit/tasks.md**：

```markdown
# 任务列表：用户资料编辑

## Task 1: 创建 API 端点
- [ ] 创建 `PUT /api/users/:id/profile` 路由
- [ ] 实现请求验证中间件
- [ ] 实现权限检查（只能编辑自己）
- [ ] 实现数据库更新逻辑
- [ ] 添加错误处理

**验收标准：**
- API 可以正确处理有效请求
- 验证失败返回 400 和错误详情
- 未登录返回 401
- 编辑他人资料返回 403

## Task 2: 创建前端组件
- [ ] 创建 `UserProfileEdit` 组件
- [ ] 实现表单 UI
- [ ] 集成表单验证
- [ ] 调用 API 保存数据
- [ ] 处理加载和错误状态

**验收标准：**
- 表单显示正确
- 验证实时反馈
- 保存成功有提示
- 保存失败显示错误

## Task 3: 添加测试
- [ ] API 单元测试
- [ ] 组件单元测试
- [ ] 集成测试

**验收标准：**
- 测试覆盖率 > 80%
- 所有测试通过
```

### 步骤 2：审查并确认

**用户审查**：
```markdown
用户查看 proposal.md、design.md、tasks.md

确认内容：
✅ 需求理解正确
✅ 设计符合预期
✅ 任务划分合理

或者提出修改：
❓ "个人网站字段改为可选项，不需要验证"

AI 更新 design.md...
```

### 步骤 3：实施变更

```bash
/openspec-apply
```

**实施过程**：

```markdown
AI: 开始实施变更 change-001...

    读取任务列表，共 3 个任务
    
    === Task 1: 创建 API 端点 ===
    
    ✓ 创建 src/routes/user.routes.ts
    ✓ 添加 PUT /api/users/:id/profile 路由
    ✓ 创建 src/middleware/validation.middleware.ts
    ✓ 实现请求体验证
    ✓ 创建 src/middleware/auth.middleware.ts
    ✓ 实现权限检查
    ✓ 更新 src/services/user.service.ts
    ✓ 添加 updateProfile 方法
    
    运行测试...
    ✓ 所有 API 测试通过
    
    === Task 2: 创建前端组件 ===
    
    ✓ 创建 src/components/UserProfileEdit.tsx
    ✓ 实现表单 UI
    ✓ 添加表单验证逻辑
    ✓ 集成 API 调用
    ✓ 添加加载和错误状态
    
    运行测试...
    ✓ 组件测试通过
    
    === Task 3: 添加测试 ===
    
    ✓ 创建 src/routes/__tests__/user.routes.test.ts
    ✓ 创建 src/components/__tests__/UserProfileEdit.test.tsx
    ✓ 创建 tests/integration/user-profile.test.ts
    
    运行所有测试...
    ✓ 15 个测试全部通过
    ✓ 覆盖率：API 87%，组件 82%
    
    === 实施完成 ===
    
    生成文件：
    - src/routes/user.routes.ts
    - src/middleware/validation.middleware.ts
    - src/middleware/auth.middleware.ts
    - src/services/user.service.ts (更新)
    - src/components/UserProfileEdit.tsx
    - 对应的测试文件
    
    建议运行：npm test 验证
```

### 步骤 4：验证

```bash
# 运行测试
npm test

# 手动验证
curl -X PUT http://localhost:3000/api/users/user-123/profile \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{
    "nickname": "张三",
    "bio": "热爱技术的开发者",
    "website": "https://example.com"
  }'
```

### 步骤 5：归档

```bash
/openspec-archive
```

**归档过程**：

```markdown
AI: 开始归档变更 change-001...

    1. 读取变更规范
       - proposal.md
       - design.md
       - tasks.md
    
    2. 更新真相源
       ✓ 创建 openspec/specs/user.spec.md
       ✓ 合并用户资料编辑功能规范
       ✓ 更新 openspec/specs/api.spec.md
       ✓ 添加 API 端点文档
    
    3. 移动变更到归档
       ✓ openspec/changes/change-001/ → openspec/changes/_archived/change-001/
    
    === 归档完成 ===
    
    真相源已更新，包含最新的用户资料编辑功能规范。
```

---

## 实战案例 2：数据库迁移

### 场景

需要迁移数据库，添加新的字段并更新索引。

### 使用探索模式

```bash
/openspec-explore
```

**探索过程**：

```markdown
用户：我需要优化用户表的数据库性能，但不确定从哪里开始

AI：让我先分析一下当前的数据库结构...

    [分析数据库 schema]
    [检查现有索引]
    [查看查询日志]
    
    发现以下问题：
    
    1. users 表缺少 email 字段索引
       - 当前登录查询需要全表扫描
       - 建议添加唯一索引
    
    2. orders 表的 user_id 查询慢
       - 缺少外键索引
       - 建议添加普通索引
    
    3. posts 表的 created_at 用于排序
       - 建议添加复合索引 (user_id, created_at)
    
    建议创建 "数据库性能优化" 变更。
    是否继续？

用户：是的，创建变更

AI：创建变更提案...
    ✓ 创建 change-002-database-optimization/
    ✓ 生成 proposal.md
```

### 生成迁移脚本

```bash
/openspec-apply
```

**AI 生成**：

```markdown
AI: 实施数据库优化...

    === 生成迁移脚本 ===
    
    ✓ 创建 migrations/20250115_add_user_email_index.sql
    ✓ 创建 migrations/20250115_add_order_user_id_index.sql
    ✓ 创建 migrations/20250115_add_posts_composite_index.sql
    
    迁移脚本内容：
    
    -- 添加 email 唯一索引
    CREATE UNIQUE INDEX CONCURRENTLY idx_users_email 
    ON users(email) 
    WHERE deleted_at IS NULL;
    
    -- 添加 orders 外键索引
    CREATE INDEX CONCURRENTLY idx_orders_user_id 
    ON orders(user_id);
    
    -- 添加 posts 复合索引
    CREATE INDEX CONCURRENTLY idx_posts_user_created 
    ON posts(user_id, created_at DESC);
    
    === 生成回滚脚本 ===
    
    ✓ 创建 migrations/20250115_rollback_indexes.sql
    
    === 更新模型 ===
    
    ✓ 更新 src/models/user.model.ts
    ✓ 更新 src/models/order.model.ts
    ✓ 更新 src/models/post.model.ts
    
    === 完成 ===
    
    请审查迁移脚本，确认后在生产环境谨慎执行。
```

---

## 实战案例 3：API 重构

### 场景

重构旧的 REST API 为新的版本，同时保持向后兼容。

### 使用快速模式

```bash
/openspec-ff-change "重构 API v1 到 v2，添加分页和过滤"
```

**一次性生成所有工件**：

```markdown
AI: 快速生成所有工件...

    ✓ proposal.md - 重构提案
    ✓ design.md - v2 API 设计
    ✓ migration.md - 迁移指南
    ✓ tasks.md - 实施任务
    
    设计要点：
    - 添加标准分页格式
    - 统一过滤参数
    - 保持 v1 兼容（通过路由）
    - 添加 deprecation header
```

### 增量实施

```bash
# 分步实施，每次一个端点
/openspec-apply --task="Task 1"

# 验证后实施下一个
/openspec-apply --task="Task 2"
```

---

## 常见问题处理

### 问题 1：AI 理解错误需求

**现象**：
```markdown
AI 生成的设计中包含了不需要的功能
```

**解决**：
```markdown
用户：这个设计包含了头像上传，但本次不需要

AI：明白，我来更新设计文档...
    
    修改 design.md：
    - 移除头像上传相关设计
    - 更新 Scope 部分明确说明
    - 更新任务列表
    
    已更新，请查看。
```

### 问题 2：实施过程中遇到技术难题

**现象**：
```markdown
AI 在实施时发现某个技术方案不可行
```

**解决**：
```markdown
AI：在实施 Task 2 时发现一个问题...
    
    原设计使用 localStorage 存储临时数据，
    但这在多标签页场景下会有同步问题。
    
    建议修改为：
    1. 使用 IndexedDB 替代 localStorage
    2. 或使用服务端临时存储
    
    或者返回探索模式重新设计？

用户：使用 IndexedDB，更新设计文档

AI：更新 design.md...
    ✓ 更新数据存储方案
    ✓ 更新任务列表
    继续实施？
```

### 问题 3：需要回滚变更

**现象**：
```markdown
实施后发现方向错误，需要重新开始
```

**解决**：
```bash
# 方法 1：删除变更重新开始
rm -rf openspec/changes/change-003/
/openspec-new-change "正确的需求描述"

# 方法 2：在现有变更上修改
# 编辑 proposal.md 重新描述需求
# 然后重新生成后续文档
/openspec-continue-change
```

---

## 团队协作实践

### 代码审查流程

```markdown
1. 开发者 A 创建变更
   /openspec-new-change "添加支付功能"

2. 开发者 A 完成设计文档
   
3. 提交 PR，包含变更目录
   git add openspec/changes/change-004/
   git commit -m "proposal: add payment feature"

4. 开发者 B 审查设计文档
   - 查看 proposal.md
   - 查看 design.md
   - 在 PR 中评论建议

5. 根据反馈修改
   - 更新文档
   - 重新提交

6. 审查通过后实施
   /openspec-apply

7. 代码审查
   - 审查生成的代码
   - 运行测试

8. 归档
   /openspec-archive
```

### 规范审查清单

```markdown
## Proposal 审查
- [ ] 需求描述清晰
- [ ] Scope 边界明确
- [ ] Success Criteria 可验证

## Design 审查
- [ ] 架构合理
- [ ] API 设计符合规范
- [ ] 数据模型完整
- [ ] 错误处理考虑周全

## Tasks 审查
- [ ] 任务划分合理
- [ ] 验收标准明确
- [ ] 依赖关系清晰
```

---

## 与现有工具集成

### Git 工作流

```bash
# 1. 创建功能分支
git checkout -b feature/user-profile

# 2. 创建变更
/openspec-new-change "用户资料功能"

# 3. 提交变更文档
git add openspec/changes/change-001/
git commit -m "docs: add user profile change proposal"

# 4. 实施
git add .
git commit -m "feat: implement user profile editing"

# 5. 归档
git add openspec/
git commit -m "docs: archive user profile change"

# 6. 合并到主分支
git checkout main
git merge feature/user-profile
```

### CI/CD 集成

```yaml
# .github/workflows/openspec.yml
name: OpenSpec Validation

on:
  pull_request:
    paths:
      - 'openspec/**'

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Validate Specs
        run: |
          # 验证规范格式
          # 检查必填字段
          # 验证链接完整性
      
      - name: Check Implementation
        run: |
          # 检查是否实施了变更
          # 运行测试验证
```

---

## 相关文档

- OpenSpec 概述与核心概念 - 基础概念
- OpenSpec 工作流程详解 - 详细工作流程
- OpenSpec 进阶与最佳实践 - 高级用法

---

*文档版本: 1.0*
*最后更新: 2025-02-10*