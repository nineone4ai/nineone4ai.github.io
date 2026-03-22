---
title: "PM3 AI工具链速查表"
date: 2026-02-25 10:00:00 +0800
categories: [软件工程]
tags: ["PM3", "AI辅助研发", "速查表"]
author: 戴晓峰
---

# PM3 AI工具链速查表

> 按场景速查：用哪个工具、输入什么、期望什么输出

---

## 场景1：新模块开发全流程

```
Step 1  拿到 PRD
         ↓ Claude Code [P1: PRD解析Prompt]
Step 2  结构化需求清单 + 歧义问题列表
         ↓ 产品确认歧义
Step 3  Claude Code [P2: -api 模块设计]
         ↓ 生成跨服务 Feign Client + DTO
Step 3b Claude Code [P2B: -svc Controller 设计]
         ↓ 生成前端 REST API 签名 + VO
         ↓ [P3: 设计评审 Checklist 检查] → 评审通过
         ↓ -api jar 发布 Nexus
Step 4  /pm3-microservice-init
         ↓ 输入模块名 → 完整代码骨架（10分钟）
Step 5  Claude Code [接口填充Prompt]
         ↓ Controller + Service 方法签名填充
Step 6  Cursor 实时补全
         ↓ 业务逻辑实现
Step 7  Claude Code [P4: AI CR Prompt]
         ↓ 代码质量报告
         ↓ 修复 → PR → Jenkins
Step 8  合并主干
```

---

## Prompt 速查库

### P1：PRD 解析

```
请分析以下 PRD，按 PM3 模块边界拆解，输出：
1. 功能点清单（编号、描述、归属模块：pm-prophase/pm-development/pm-engineering/其他）
2. 需要新增或修改的接口列表（初步）
3. 依赖的基础服务（pm-platform权限点 / pm-auth认证 / pm-bpm工作流）
4. 歧义问题清单（不明确的业务规则，需产品澄清）

PRD 内容：
[粘贴PRD内容]
```

---

### P2：-api 模块设计（跨服务调用契约）

> **适用范围**：设计当前服务对外暴露给**其他微服务**的 Feign Client 接口。`-api` 模块打包成 jar 发布 Nexus，供 Consumer 服务 Maven 依赖引入。**不用于定义前后端交互接口**（前端 API 见 P2B）。

```
请根据以下功能需求，生成符合 PM3 规范的 -api 模块代码骨架：

模块名：pm-{name}
功能：[描述]
对外提供的跨服务能力：[其他服务会调用的功能]
依赖的外部服务：[pm-bpm / pm-platform 等]

规范要求：
- Feign Client：I{Name}Client，包路径 com.sungrow.pm.{name}.client
- DTO：*DTO（跨服务数据对象），包路径 com.sungrow.pm.{name}.dto
  注意：VO（前端视图对象）定义在 -svc 模块，不属于 -api
- 路径：/api/pm-{name}/v3/{resource}
- RestResponse<T> 统一响应体
- @Permission("module:resource:action") 权限注解建议

输出：Feign Client 接口 + 主要 DTO 类
```

---

### P2B：-svc Controller 设计（前后端交互接口）

> **适用范围**：设计当前服务暴露给**前端/移动端**的 REST API，通过 pm-gateway 路由，由 `-svc` 模块 Controller 实现。与 `-api` 模块完全独立。

```
请根据以下功能需求，生成符合 PM3 规范的 -svc Controller 接口设计：

模块名：pm-{name}
功能：[描述]
前端需要的操作：[列出功能点，如：分页查询/新增/修改/删除等]

规范要求：
- Controller 类命名：{业务名}Controller
- REST 路径：/api/pm-{name}/v3/{resource}
- VO 命名：*VO（前端请求/响应对象），包路径 com.sungrow.pm.{name}.vo
- 统一响应体：RestResponse<T>
- Swagger 注解：@Operation(summary = "接口说明")
- 权限注解：@Permission("module:resource:action")
- 参考 PM3 现有 Controller 风格（[粘贴现有示例]）

输出：Controller 方法签名 + VO 类定义 + Swagger 注解骨架
```

---

### P3：设计评审 Checklist 自动检查

```
请检查以下 PM3 模块设计，对照 PM3 设计评审门禁 Checklist 输出检查结果：

【-api 模块检查项（跨服务接口契约）】
□ Feign Client 命名：I{Name}Client，包路径正确？
□ DTO 命名：*DTO，字段完整（含注释、类型、是否必填）？
□ 接口文档完整性：参数/响应/错误码均有说明？
□ 幂等性：POST/PUT 是否有幂等设计？
□ 权限码：@Permission 注解建议命名？

【-svc Controller 检查项（前后端交互接口）】
□ Controller 命名规范：{业务名}Controller？
□ VO 命名：*VO，字段完整？
□ Swagger 注解：@Operation / @Parameter 完整？
□ pm-gateway 路由规则是否已注册？
□ Nacos 配置：是否有需要注册的配置项？

【Entity 检查项（-svc 模块）】
□ DB 审计字段：deleteFlag/createBy/createTime/updateBy/updateTime？
□ 软删除：使用 deleteFlag="0/1"（非 isDeleted）？

代码/文档：
[粘贴 -api 模块代码 或 -svc Controller/Entity 代码]
```

---

### P4：AI Code Review

```
请 Review 以下 PM3 代码变更，输出问题清单（按 P0/P1/P2 优先级）：

P0（必须修复）：
- 缺失 @Permission 权限注解
- SQL 注入风险
- 缺失事务注解（写操作）
- Entity 缺少审计字段

P1（建议修复）：
- 命名不符合 PM3 规范
- 潜在 NPE
- 异常未捕获/处理

P2（可选优化）：
- 可抽取公共方法
- 注释不完整

变更内容：
[粘贴 diff 或代码]
```

---

### P5：Activiti 工作流生成

```
请根据以下审批流程描述，生成 Activiti 7.x 兼容的 BPMN 2.0 XML（.bpmn20.xml）：

流程名称：[中文名称]
流程Key：[英文Key]

节点定义：
1. [节点名] - [类型：startEvent/userTask/exclusiveGateway/parallelGateway/endEvent]
   - 审批人/处理人：[变量名或固定值]
   - 条件：[如果是网关，列出条件表达式]

参考 Activiti 7.1.0.M6 语法。
```

---

### P6：MyBatis XML SQL 生成

```
请根据以下信息，生成 PM3 风格的 MyBatis XML 查询：

Entity 类：[粘贴 Entity 代码]
查询需求：[描述查询条件、关联表、排序、分页等]

要求：
- 使用 MyBatis-Plus 风格（BaseMapper 扩展）
- 动态SQL用 <if test="..."> 处理可选条件
- 软删除过滤：deleteFlag = '0'
- 如需关联查询，使用 LEFT JOIN
```

---

### P7：Bug 排查

```
以下是 PM3 生产/测试环境的异常，请分析根因并给出修复方案：

服务名：[pm-xxx]
环境：[生产/测试]
异常类型：[NullPointerException / 业务异常 / 超时 等]
异常日志：
[粘贴日志/堆栈]

相关代码（可选）：
[粘贴可疑代码段]

输出：
1. 根因分析
2. 修复方案（含代码示例）
3. 预防措施
```

---

### P8：影响分析（-api 变更）

```
以下 PM3 服务接口即将变更，请分析对 Consumer 服务的影响：

变更服务：[pm-bpm / pm-platform / 其他]
变更描述：[新增字段 / 删除字段 / 修改方法签名 等]
是否 Breaking Change：[是/否/不确定]

Consumer 代码（需要检查的调用方）：
[粘贴 Feign Client 调用代码]

输出：
1. 受影响的调用位置清单
2. 每处的修改方案
3. 是否需要升级 -api 版本号（语义化版本建议）
```

---

### P9：存量代码理解与文档化

```
请为以下 PM3 存量代码生成文档：

[粘贴复杂的 ServiceImpl 方法或核心业务类]

输出：
1. 方法级 JavaDoc 注释（中文）
2. 关键步骤内联注释
3. 业务流程摘要（3-5句话，面向新加入的开发者）
4. 潜在风险点（如果有）
```

---

### P10：运维手册生成

```
请为以下 PM3 微服务生成运维手册：

服务名：[pm-xxx]
技术栈：Spring Boot 2.x + Nacos + Sentinel
端口：[xxx]

已知信息：
- 主要功能：[描述]
- 外部依赖：[MySQL / Redis / pm-bpm 等]
- 关键配置项：[列出 Nacos 配置键]

请生成包含以下内容的运维手册：
1. 服务启动/停止/重启命令
2. 健康检查端点
3. 常见告警及处理方案
4. 日志位置和关键日志模式
5. 回滚方式
```

---

## 工具矩阵

| 任务 | Claude Code | Cursor | pm3-init Skill |
|------|:-----------:|:------:|:--------------:|
| PRD 解析 | ✅ 首选 | ❌ | ❌ |
| -api 模块设计（跨服务 Feign Client） | ✅ 首选 | ⬜ 辅助 | ❌ |
| -svc Controller 设计（前端 REST API） | ✅ 首选 | ✅ 辅助 | ❌ |
| 新模块骨架 | ❌ | ❌ | ✅ 首选 |
| 骨架内容填充 | ✅ 首选 | ✅ 辅助 | ❌ |
| 业务逻辑实现 | ⬜ 复杂逻辑 | ✅ 首选 | ❌ |
| Activiti BPMN | ✅ 首选 | ❌ | ❌ |
| MyBatis SQL | ✅ 首选 | ✅ 辅助 | ❌ |
| Code Review | ✅ 首选 | ❌ | ❌ |
| 单元测试 | ⬜ 辅助 | ✅ 首选 | ❌ |
| Bug 排查 | ✅ 首选 | ❌ | ❌ |
| 影响分析 | ✅ 首选 | ❌ | ❌ |
| 代码文档化 | ✅ 首选 | ❌ | ❌ |

## 相关概念

- AI辅助研发工作流 — 完整方法论
- 设计评审门禁 — P3 检查项的详细说明
- CI-CD质量卡口 — Jenkins 与 AI CR 的集成