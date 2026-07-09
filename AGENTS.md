# Zero Agent Project Rules

本项目是一个面向前后端调用的 Agent Harness / Skill Runtime 系统，不是普通聊天机器人。

## 文档分层

- `docs/architecture/agent-harness/`：系统架构设计文档，回答“系统如何设计”。
- `docs/ai-context/`：AI 开发规范文档，回答“opencode Agent 如何读取规范、开发和验收”。
- `frontend/AGENTS.md`：前端 opencode 工作区规则。
- `backend/AGENTS.md`：后端 opencode 工作区规则。

## 工作原则

1. 开发前先读 `docs/ai-context/00-doc-index.md`。
2. 根据任务类型读取最小必要文档，不要一次性读取所有文档。
3. 架构相关任务必须优先读取 `docs/architecture/agent-harness/README.md` 和相关架构章节。
4. API、数据库、Web Service / BFF 相关任务必须读取：
   - `docs/architecture/agent-harness/10-web-service-bff.md`
   - `docs/architecture/agent-harness/11-database-design.md`
   - `docs/architecture/agent-harness/12-api-design.md`
   - `docs/ai-context/02-api-contract.md`
5. 前后端接口字段以 `docs/ai-context/02-api-contract.md` 为执行契约。
6. 不要修改无关文件。
7. 不要凭空创造接口字段、数据库字段、权限码、枚举值。
8. 如果架构文档和接口契约冲突，先停止开发并报告冲突。
9. 每次开发完成后必须输出：修改文件、影响范围、验证步骤、未完成事项。

## 推荐流程

```text
架构设计 -> API 契约 -> 后端实现 -> 前端接入 -> Review -> 测试
```

## 禁止事项

- 禁止前端自己造字段。
- 禁止后端私自改响应结构。
- 禁止把架构文档当成最终接口契约；最终执行以 `02-api-contract.md` 为准。
- 禁止一次任务大范围重构不相关模块。