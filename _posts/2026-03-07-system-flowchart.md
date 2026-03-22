---
title: "系统分析中的流程图设计"
date: 2026-03-07 10:00:00 +0800
categories: [软件工程]
tags: ["流程图", "时序图", "ER图", "Mermaid"]
author: 戴晓峰
---

# 系统分析中的流程图设计

> 在系统分析文档中，使用流程图从不同视角描述系统行为

---

## 三种核心流程图

### 1. 业务流程图（用户操作视角）

描述用户如何操作系统，完成业务目标

**适用场景**：
- 描述用户操作流程
- 培训文档
- 用户手册

**示例**：
```mermaid
flowchart TD
    A[进入用户列表] --> B{选择操作}
    B -->|新建 | C[填写表单]
    B -->|编辑 | D[选择用户并编辑]
    B -->|删除 | E[确认删除]
    C --> F[保存]
    D --> F
    E --> G[执行删除]
    F --> H[返回列表]
    G --> H
```

### 2. 数据流程图（系统处理视角）

描述数据在系统中的流转和处理过程

**适用场景**：
- 描述系统内部处理逻辑
- 数据流转分析
- 接口设计参考

**示例**：
```mermaid
flowchart LR
    A[用户输入] --> B[参数校验]
    B --> C[业务处理]
    C --> D[数据持久化]
    D --> E[返回结果]
```

### 3. 时序图（交互视角）

描述对象/组件之间的交互时序

**适用场景**：
- 接口交互设计
- 前后端协作
- 复杂流程的时序分析

**示例**：
```mermaid
sequenceDiagram
    participant U as 用户
    participant F as 前端
    participant B as 后端
    participant D as 数据库

    U->>F: 提交表单
    F->>F: 表单校验
    F->>B: 发送请求
    B->>B: 业务校验
    B->>D: 保存数据
    D-->>B: 返回结果
    B-->>F: 返回响应
    F-->>U: 展示结果
```

---

## 绘制技巧

### 先主后次

1. 先画主流程（Happy Path）
2. 再画分支流程
3. 最后画异常流程

### 角色区分

用不同颜色或泳道区分不同角色/系统：

```mermaid
flowchart TB
    subgraph 用户
        A1[发起申请]
        A2[补充材料]
    end

    subgraph 系统
        B1[自动校验]
        B2[流转审批]
    end

    subgraph 审核人
        C1[审核]
    end

    A1 --> B1
    B1 --> B2
    B2 --> C1
```

### 状态标注

在流程中标注状态变化：

```mermaid
flowchart TD
    A[待提交] -->|提交 | B[待审核]
    B -->|审核通过 | C[已通过]
    B -->|审核驳回 | D[已驳回]
    D -->|重新提交 | B
```

---

## 状态流转图

用于描述对象状态的变化：

```mermaid
stateDiagram-v2
    [*] --> 待提交
    待提交 --> 待审核：提交
    待审核 --> 已通过：审核通过
    待审核 --> 已驳回：审核驳回
    已驳回 --> 待审核：重新提交
    已通过 --> [*]
```

---

## ER 图（实体关系图）

用于描述数据模型：

```mermaid
erDiagram
    USER {
        bigint id PK
        string username
        bigint dept_id FK
    }

    DEPT {
        bigint id PK
        string dept_name
    }

    USER ||--|| DEPT : "属于"
```

---

## 类图（对象模型）

用于描述代码结构：

```mermaid
classDiagram
    class User {
        -Long id
        -String username
        +getName() String
    }

    class UserService {
        +create(User) Result
        +getById(Long) User
    }

    User --> UserService : 使用
```

---

## 工具推荐

- **Mermaid**：文本绘图，集成在 Markdown 中
- **Draw.io**：图形化绘图，支持导出多种格式
- **PlantUML**：专业的 UML 绘图工具

---

## 相关工作

- PRD 系统分析 - 系统分析文档的完整内容
- PRD 分析方法论 - 完整的 PRD 分析流程
- [Mermaid 语法教程](https://mermaid.js.org/intro/)

---

## 模板位置

- `99_系统/模板/PRD 分析/` 中的各模板都包含流程图示例