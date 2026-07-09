# Enterprise Agent Harness + Skill Runtime

本目录是一套企业级 Agent 平台开发文档草案，适合放入项目仓库的 `docs/agent-harness/` 目录。

目标不是做一个普通聊天机器人，而是建设一个可规划、可执行、可记忆、可审计、可恢复、可扩展的 Agent Runtime。

## 文档目录

| 文件 | 说明 |
|---|---|
| `01-overview.md` | 总体目标、核心概念、系统分层 |
| `02-agent-runtime-core.md` | Agent Runtime 核心链路设计 |
| `03-planner-design.md` | Planner 任务规划器设计 |
| `04-context-builder.md` | 上下文工程设计 |
| `05-tool-runtime.md` | Tool Registry 与工具执行设计 |
| `06-skill-runtime.md` | Skill Runtime 技能运行时设计 |
| `07-memory-system.md` | 记忆系统设计 |
| `08-task-state-machine.md` | 任务状态机与执行流 |
| `09-security-approval-audit.md` | 安全、审批、审计设计 |
| `10-web-service-bff.md` | Web 服务 / BFF / 前端调用层设计 |
| `11-database-design.md` | 数据库表结构设计 |
| `12-api-design.md` | 核心接口设计 |
| `13-mvp-roadmap.md` | MVP 开发路线和任务拆解 |

## 推荐落地顺序

1. 先实现 `Agent Runtime Core`：Router、Planner、Executor、Tool Runtime。
2. 再实现 `Task State Machine` 和 `Audit Log`，保证任务可追踪。
3. 然后做 `Skill Runtime`，把高频业务流程沉淀成技能。
4. 最后做 `Memory`、`Approval`、`Web 管理台`、`长期任务调度`。

## 推荐部署结构

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

## 核心原则

```text
LLM 负责推理。
Runtime 负责执行。
Tool 负责确定性能力。
Skill 负责业务流程沉淀。
Audit 负责留痕。
Approval 负责高风险操作控制。
Memory 负责长期变聪明。
```