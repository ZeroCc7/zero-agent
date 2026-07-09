# Project Overview

## 项目定位

Zero Agent 是一个面向真实业务系统的 Agent Harness / Skill Runtime 平台。目标不是做一个普通聊天机器人，而是建设一套可规划、可执行、可记忆、可审计、可恢复、可扩展，并且可以通过 Web API 给前端调用的 Agent 系统。

详细架构见：

- `../architecture/agent-harness/README.md`
- `../architecture/agent-harness/01-overview.md`

## 核心目标

- 支持用户通过前端发起 Agent 任务。
- 后端 Web Service / BFF 接收请求并创建任务。
- Agent Runtime 负责规划、上下文构建、工具调用、技能执行、状态持久化。
- Tool Runtime 提供确定性外部能力。
- Skill Runtime 将高频业务流程沉淀成可复用技能。
- Task State Machine 保证任务可追踪、可恢复。
- Audit / Approval 负责高风险操作控制和审计留痕。
- Memory System 让 Agent 具备长期上下文和偏好记忆能力。

## 推荐系统分层

```text
frontend-web
  ↓
agent-web-bff
  ↓
agent-runtime
  ↓
agent-worker
  ↓
tool-services / business-services / mcp-servers / databases / vector-store
```

## 文档使用规则

本项目文档分两层：

| 文档类型 | 位置 | 作用 |
|---|---|---|
| 架构文档 | `docs/architecture/agent-harness/` | 指导系统设计、模块边界、运行机制 |
| 开发规范 | `docs/ai-context/` | 指导 opencode Agent 如何开发、联调、Review |

开发时不要把所有文档一次性读完。先读 `00-doc-index.md`，再按任务类型读取最小必要文档。

## 前后端协作原则

- API 契约是前后端唯一执行依据。
- 架构文档中的 API 设计是设计参考，落地时需要同步到 `02-api-contract.md`。
- 前端不能私自创造字段。
- 后端不能私自修改响应结构。
- 长任务、流式输出、审批、审计等能力必须在接口契约里明确。

## Agent 开发原则

```text
LLM 负责推理。
Runtime 负责执行。
Tool 负责确定性能力。
Skill 负责业务流程沉淀。
Audit 负责留痕。
Approval 负责高风险操作控制。
Memory 负责长期变聪明。
```