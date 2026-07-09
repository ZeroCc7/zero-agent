# Frontend Agent Rules

你正在 `frontend/` 工作区开发 Zero Agent 前端。

## Scope

- 只修改 `frontend/` 下的文件，除非用户明确要求修改共享文档。
- 不要修改 `backend/` 文件。
- 不要凭空创造 API 字段。
- API 字段必须遵循 `../docs/ai-context/02-api-contract.md`。
- 如果 API 契约缺失或不清楚，先停止开发并提出契约更新建议。

## Required Reading

开发前先读：

- `../docs/ai-context/00-doc-index.md`
- `../docs/ai-context/01-project-overview.md`
- `../docs/ai-context/02-api-contract.md`
- `../docs/ai-context/03-frontend-rules.md`
- `../docs/ai-context/05-ui-interaction-rules.md`
- `../docs/ai-context/07-permission-rules.md`

如果任务涉及 Agent 任务流、流式输出、审批、工具调用、技能执行，还需要读取：

- `../docs/architecture/agent-harness/10-web-service-bff.md`
- `../docs/architecture/agent-harness/12-api-design.md`
- `../docs/architecture/agent-harness/08-task-state-machine.md`
- `../docs/architecture/agent-harness/09-security-approval-audit.md`

## Frontend Requirements

每个页面或组件必须考虑：

- loading 状态
- empty 状态
- error 状态
- permission 状态
- 表单校验
- 长任务状态展示
- SSE / WebSocket / 轮询的生命周期清理
- 审批等待状态
- 工具调用过程展示

## API Rules

- 前端只能使用 `02-api-contract.md` 中已经定义的字段。
- 如果架构文档中有接口，但 `02-api-contract.md` 没有，先补契约，不要直接接入。
- 所有枚举值、错误码、分页结构必须与契约一致。

## Output

完成后输出：

- 修改文件
- 使用的 API
- 使用的字段
- 涉及的状态处理
- 自测步骤
- 未完成事项或契约缺口