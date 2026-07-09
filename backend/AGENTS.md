# Backend Agent Rules

你正在 `backend/` 工作区开发 Zero Agent 后端。

## Scope

- 只修改 `backend/` 下的文件，除非用户明确要求修改共享文档。
- 不要修改 `frontend/` 文件。
- API 行为必须遵循 `../docs/ai-context/02-api-contract.md`。
- 不要私自修改响应字段、错误码、分页结构或状态枚举。

## Required Reading

开发前先读：

- `../docs/ai-context/00-doc-index.md`
- `../docs/ai-context/01-project-overview.md`
- `../docs/ai-context/02-api-contract.md`
- `../docs/ai-context/04-backend-rules.md`
- `../docs/ai-context/06-database-rules.md`
- `../docs/ai-context/07-permission-rules.md`
- `../docs/ai-context/08-error-handling-rules.md`
- `../docs/ai-context/09-testing-rules.md`

如果任务涉及 Agent Runtime / Harness 核心模块，还需要读取对应架构文档：

- `../docs/architecture/agent-harness/01-overview.md`
- `../docs/architecture/agent-harness/02-agent-runtime-core.md`
- `../docs/architecture/agent-harness/03-planner-design.md`
- `../docs/architecture/agent-harness/04-context-builder.md`
- `../docs/architecture/agent-harness/05-tool-runtime.md`
- `../docs/architecture/agent-harness/06-skill-runtime.md`
- `../docs/architecture/agent-harness/07-memory-system.md`
- `../docs/architecture/agent-harness/08-task-state-machine.md`
- `../docs/architecture/agent-harness/09-security-approval-audit.md`
- `../docs/architecture/agent-harness/10-web-service-bff.md`
- `../docs/architecture/agent-harness/11-database-design.md`
- `../docs/architecture/agent-harness/12-api-design.md`

## Backend Requirements

每个 API 或服务必须考虑：

- 请求参数校验
- 权限校验
- 业务异常
- 统一响应结构
- 分页结构
- 事务边界
- 任务状态流转
- 审计日志
- 高风险操作审批
- 工具调用失败恢复
- 测试或最小验证步骤

## API Rules

- 架构文档是设计参考，`02-api-contract.md` 是前后端执行契约。
- 如果实现需要新增或修改接口，先更新 `02-api-contract.md`。
- 不要只改后端代码而不更新契约。

## Output

完成后输出：

- 修改文件
- 实现的 API
- 数据库影响
- 状态机影响
- 安全 / 审计影响
- 验证结果
- 未完成事项或契约缺口