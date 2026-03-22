---
title: "契约先行开发"
date: 2026-02-24 10:00:00 +0800
categories: [软件工程]
tags: ["微服务", "测试", "契约测试", "API设计"]
author: 戴晓峰
---

# 契约先行开发

## 定义

契约先行开发（Contract-First Development，又称 API-First）是一种**在编写任何实现代码之前，先定义并冻结服务间接口契约**的开发模式。

在微服务并行开发场景下，契约文件是唯一的"服务间协议"，Consumer 和 Provider 双方各自对照契约独立开发和测试，无需等待对方完成才能联调。

## 要点

- **契约 = 双方的唯一真相来源**：接口格式、请求/响应字段、状态码、错误结构，全部在契约文件中定义
- **Consumer 驱动**：由调用方（Consumer）定义期望的接口行为，Provider 实现并验证
- **Mock 替代真实依赖**：Provider 未完成前，Consumer 可基于契约生成 Mock 独立开发
- **契约变更需双方确认**：任何字段变更必须更新契约文件，触发双方重新验证

## Spring Cloud Contract 用法（Java 生态）

### 1. 定义契约（Groovy DSL）

```groovy
// src/test/resources/contracts/getUser.groovy
Contract.make {
    request {
        method GET()
        url '/api/users/1'
    }
    response {
        status 200
        body([
            id: 1,
            name: '张三',
            status: 'active'
        ])
        headers {
            contentType(applicationJson())
        }
    }
}
```

### 2. Provider 侧验证

```java
@SpringBootTest
@AutoConfigureStubRunner
public class UserControllerContractTest extends UserControllerBase {
    // Spring Cloud Contract 自动生成测试，对照契约验证接口实现
}
```

### 3. Consumer 侧使用 Stub

```java
@SpringBootTest
@AutoConfigureStubRunner(
    ids = "com.example:user-service:+:stubs:8080",
    stubsMode = StubRunnerProperties.StubsMode.LOCAL
)
public class OrderServiceTest {
    // 基于 Stub 测试，无需真实 user-service 运行
}
```

## 在微服务并行开发中的价值

| 场景 | 无契约先行 | 有契约先行 |
|------|-----------|-----------|
| 并行开发 | 互相等待，阻塞 | 各自对照契约独立开发 |
| 联调时机 | 集成阶段才发现不一致 | 契约测试在开发阶段持续运行 |
| 接口变更 | 口头/IM沟通，易遗漏 | 契约文件变更 + CI自动验证 |
| 需求对齐 | 开发理解各异 | 契约即对齐文档 |

## 与 Activiti 工作流引擎的集成

Activiti 作为共享基础设施，应**优先完成契约定义**：

1. Activiti 专属团队在第1-2周完成所有对外API的契约文件
2. 发布契约 Stub，其他5个新服务基于 Stub 开发工作流调用逻辑
3. Activiti 实现完成后，契约测试自动验证 Stub 与真实实现一致

## 相关概念

- 设计评审门禁 — 契约文件应作为设计评审的核心产出物
- CI-CD质量卡口 — 契约测试作为 pipeline 的必通关卡
- 微服务架构 — 契约先行是微服务并行开发的核心实践

## 参考资料

- Spring Cloud Contract 官方文档
- Consumer-Driven Contract Testing（Pact 规范）
- Martin Fowler - Integration Contract Test