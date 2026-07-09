# Integration Notes

本文件说明架构文档和 AI 开发规范文档如何配合使用。

## 为什么要分开

- 架构文档负责系统设计，内容较大，不适合每次开发全部加载。
- 开发规范负责约束 Agent 行为，内容较短，适合常驻或按任务加载。

## 执行优先级

当文档之间出现冲突时，按以下顺序处理：

1. 用户当前明确要求
2. `docs/ai-context/02-api-contract.md`
3. 当前代码中的真实实现
4. `docs/architecture/agent-harness/` 架构设计
5. 其他开发规范文档

如果冲突会影响接口、数据库或任务状态机，不要直接猜，先报告冲突。

## 常见场景

### 新增 Agent 任务创建接口

读取：

- `docs/architecture/agent-harness/10-web-service-bff.md`
- `docs/architecture/agent-harness/12-api-design.md`
- `docs/ai-context/02-api-contract.md`
- `docs/ai-context/04-backend-rules.md`
- `docs/ai-context/08-error-handling-rules.md`

执行顺序：

```text
补充 API 契约 -> 后端实现 -> 前端接入 -> Review -> 测试
```

### 新增工具调用能力

读取：

- `docs/architecture/agent-harness/05-tool-runtime.md`
- `docs/architecture/agent-harness/09-security-approval-audit.md`
- `docs/architecture/agent-harness/11-database-design.md`
- `docs/ai-context/04-backend-rules.md`

### 新增前端任务运行页

读取：

- `docs/architecture/agent-harness/08-task-state-machine.md`
- `docs/architecture/agent-harness/10-web-service-bff.md`
- `docs/ai-context/02-api-contract.md`
- `docs/ai-context/03-frontend-rules.md`
- `docs/ai-context/05-ui-interaction-rules.md`

## 最重要的规则

不要让 Agent 自由发挥系统设计。先让它读架构文档确定方向，再读开发规范确定边界，最后读代码确定落点。