# Zero Agent 文档体系

本目录整合了两类文档：

1. **架构文档**：`docs/architecture/agent-harness/`  
   用于指导 Zero Agent / Agent Harness 的系统设计、运行时、任务状态机、工具运行时、技能运行时、记忆系统、安全审计、Web Service / BFF、数据库与 API 设计。

2. **AI 开发规范文档**：`docs/ai-context/`  
   用于指导 opencode 在前端、后端两个独立工作区内稳定开发，明确 Agent 每次任务应该读取哪些规范、哪些文件可以修改、接口契约以哪里为准、如何做前后端一致性 Review。

## 推荐项目结构

```text
zero-agent/
├── docs/
│   ├── architecture/
│   │   └── agent-harness/
│   └── ai-context/
├── frontend/
│   ├── AGENTS.md
│   └── opencode.json
├── backend/
│   ├── AGENTS.md
│   └── opencode.json
└── AGENTS.md
```

## 两套文档的关系

```text
架构文档：回答“系统应该怎么设计”。
开发规范：回答“AI 应该怎么读规范、怎么改代码、怎么验收”。
```

架构文档是上层设计，开发规范是执行约束。前端和后端 opencode 都不能只看自己的代码，也不能只看 API 文档；它们需要按任务类型读取最小必要文档。

## 推荐 opencode 使用方式

前端：

```bash
cd frontend
opencode
```

后端：

```bash
cd backend
opencode
```

开发前先让 Agent 进入计划模式：

```text
请先读取 ../docs/ai-context/00-doc-index.md，根据任务类型读取最小必要文档，不要修改代码，先输出实现计划。
```

执行开发时再明确：

```text
按刚才计划执行，不要修改计划外文件。完成后输出修改文件清单和验证步骤。
```

## 核心协作原则

- 架构变更先更新 `docs/architecture/agent-harness/`。
- 前后端接口变更先更新 `docs/ai-context/02-api-contract.md`。
- 后端实现接口时参考 `docs/architecture/agent-harness/10-web-service-bff.md`、`11-database-design.md`、`12-api-design.md`。
- 前端接入接口时参考 `docs/ai-context/02-api-contract.md`、`05-ui-interaction-rules.md`、`07-permission-rules.md`。
- Review 时使用 `docs/ai-context/10-review-checklist.md`。

## 文档入口

- 架构入口：`docs/architecture/agent-harness/README.md`
- Agent 读取入口：`docs/ai-context/00-doc-index.md`
- 前端规则入口：`frontend/AGENTS.md`
- 后端规则入口：`backend/AGENTS.md`
