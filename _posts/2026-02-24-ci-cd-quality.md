---
title: "CI/CD 质量卡口"
date: 2026-02-24 10:00:00 +0800
categories: [软件工程]
tags: ["CI-CD", "质量管理", "Jenkins", "DevOps"]
author: 戴晓峰
---

# CI/CD 质量卡口

## 定义

CI/CD 质量卡口是在持续集成流水线中设置的**自动化阻断机制**：当代码提交不满足预设质量标准时，流水线自动拒绝合并或部署，强制开发者修复后才能继续。

核心原则：**质量标准由工具执行，而不依赖人工自律**。这解决了 Code Review 经常被跳过、测试覆盖率无人监督的根本问题。

## PR 合并前置条件（Jenkins + GitHub/GitLab）

```
PR 提交
  ↓
[卡口1] 代码静态检查（Checkstyle / SonarQube）
  ↓ 通过
[卡口2] 单元测试全部通过
  ↓ 通过
[卡口3] 测试覆盖率 ≥ 阈值（JaCoCo）
  ↓ 通过
[卡口4] Code Review：至少1名指定 Reviewer 通过
  ↓ 通过
[卡口5] 契约测试通过（Spring Cloud Contract）
  ↓ 通过
允许合并主干
```

> **任意卡口失败 → 自动阻断，禁止合并**

## Jenkins Pipeline 覆盖率卡口配置

```groovy
pipeline {
    stages {
        stage('Test') {
            steps {
                sh 'mvn test jacoco:report'
            }
            post {
                always {
                    jacoco(
                        execPattern: '**/target/jacoco.exec',
                        minimumLineCoverage: '80',      // 阶段2目标
                        minimumBranchCoverage: '70'
                    )
                }
            }
        }
        stage('Contract Test') {
            steps {
                sh 'mvn spring-cloud-contract:generateStubs'
                sh 'mvn test -Dtest=ContractVerificationTests'
            }
        }
    }
}
```

## 分阶段覆盖率目标

| 阶段 | 时间 | 行覆盖率目标 | 分支覆盖率目标 |
|------|------|------------|--------------|
| 核心开发期中检查 | 第5周 | ≥ 60% | ≥ 50% |
| 核心开发期结束 | 第8周 | ≥ 80% | ≥ 70% |
| 集成验收期结束 | 第10周 | ≥ 95% | ≥ 85% |

> **说明：** 覆盖率目标逐步提升，避免开发早期过度追求覆盖率而写无效测试

## 上线 Checklist（发布准备期）

```markdown
## 上线前质量卡口

### 代码质量
- [ ] 主干最新提交通过所有 CI 卡口
- [ ] SonarQube 无 Blocker / Critical 级别问题
- [ ] 单测覆盖率 ≥ 95%（行）/ ≥ 85%（分支）

### 测试覆盖
- [ ] 所有 P0 业务场景 E2E 测试通过
- [ ] 契约测试全部绿灯
- [ ] 性能测试：P99 响应时间符合 SLA
- [ ] P0 bug 清零，P1 bug 有跟踪计划

### 发布准备
- [ ] 灰度发布配置验证（5% 流量切换测试）
- [ ] 回滚预案演练通过（回滚时间 < 5分钟）
- [ ] 线上监控告警配置完成
- [ ] Nacos 配置中心检查（生产环境配置与测试环境隔离）
```

## Code Review 执行规范

Code Review 不是"可选的好习惯"，而是 **pipeline 的阻断条件**：

- PR 必须指定至少1名 Reviewer（建议：服务负责人或架构师）
- Reviewer 需在48小时内响应（超时自动发送提醒）
- Review 重点：接口契约是否符合设计文档、异常处理是否完整、是否有潜在性能问题

## 相关概念

- 契约先行开发 — 契约测试作为 pipeline 必通关卡
- 设计评审门禁 — pipeline 卡口的前置保障
- 软件工程