# AI Context Document Index

本文件是 Zero Agent 项目的 Agent 文档路由表。所有 opencode Agent 在开发前都应该先读取本文件，然后根据任务类型读取最小必要文档。

## 文档体系

本项目有两类文档：

| 类型 | 目录 | 作用 |
|---|---|---|
| 架构文档 | `docs/architecture/agent-harness/` | 说明 Agent Harness / Skill Runtime 怎么设计 |
| 开发规范 | `docs/ai-context/` | 说明 opencode Agent 怎么读取规范、开发和验收 |

## 核心原则

- 不要一开始读取所有文档。
- 先判断任务类型，再读取对应规范。
- 先计划，后执行。
- 不要修改无关文件。
- 不要凭空创造接口字段、枚举值、权限规则或数据库字段。
- 如果架构文档、API 契约、代码实现之间冲突，先停止开发并报告冲突。
- 架构文档用于设计参考，具体接口执行以 `02-api-contract.md` 为准。

## 常驻文档

建议前端和后端 Agent 默认读取：

- `00-doc-index.md`：文档路由表
- `01-project-overview.md`：项目总览
- `02-api-contract.md`：前后端共享接口契约

## 架构文档入口

- `../architecture/agent-harness/README.md`：架构文档总览
- `../architecture/agent-harness/01-overview.md`：总体架构设计
- `../architecture/agent-harness/13-mvp-roadmap.md`：MVP 开发路线

## 任务类型路由

### 1. Agent Runtime / Harness 核心开发

适用于：Agent 执行链路、Runtime、Executor、Planner、Context Builder、Tool Runtime、Skill Runtime、Memory、任务状态机。

读取：

- `../architecture/agent-harness/01-overview.md`
- `../architecture/agent-harness/02-agent-runtime-core.md`
- `../architecture/agent-harness/03-planner-design.md`
- `../architecture/agent-harness/04-context-builder.md`
- `../architecture/agent-harness/05-tool-runtime.md`
- `../architecture/agent-harness/06-skill-runtime.md`
- `../architecture/agent-harness/07-memory-system.md`
- `../architecture/agent-harness/08-task-state-machine.md`
- `04-backend-rules.md`
- `09-testing-rules.md`

开发前必须确认：

- 当前任务属于哪个 Runtime 模块
- 输入、输出、状态流转和失败恢复机制
- 是否需要审计日志
- 是否需要人工审批
- 是否影响 API 契约
- 是否影响数据库表结构

### 2. Web Service / BFF / API 开发

适用于：前端调用的 Agent Web 服务、BFF、任务创建、任务查询、工具调用、技能执行、会话接口。

读取：

- `../architecture/agent-harness/10-web-service-bff.md`
- `../architecture/agent-harness/12-api-design.md`
- `02-api-contract.md`
- `04-backend-rules.md`
- `08-error-handling-rules.md`
- `09-testing-rules.md`

开发前必须确认：

- API 路径和方法
- 请求参数
- 响应结构
- SSE / WebSocket / 普通 HTTP 的返回方式
- 鉴权和权限要求
- 任务 ID、会话 ID、运行 ID 等关键字段
- 错误码和异常处理

### 3. 数据库 / 状态持久化开发

适用于：任务表、运行记录、审计日志、工具调用日志、记忆表、技能表、数据库迁移。

读取：

- `../architecture/agent-harness/11-database-design.md`
- `06-database-rules.md`
- `04-backend-rules.md`
- `09-testing-rules.md`

开发前必须确认：

- 表结构变更
- 索引设计
- 兼容性影响
- 是否需要迁移脚本
- 是否影响已有 API
- 是否影响任务恢复和审计追踪

### 4. 前端页面开发

适用于：页面、路由、组件、表格、表单、弹窗、筛选、前端状态、UI 调整。

读取：

- `03-frontend-rules.md`
- `05-ui-interaction-rules.md`
- `07-permission-rules.md`
- `02-api-contract.md`
- `../architecture/agent-harness/10-web-service-bff.md`（涉及 Agent 任务流时读取）
- `../architecture/agent-harness/12-api-design.md`（涉及核心 API 时读取）

开发前必须确认：

- 页面路由
- 页面模块
- 使用的 API
- 请求参数
- 响应字段
- loading / empty / error 状态
- 权限按钮或权限视图
- 表单校验规则
- 长任务是否需要轮询、SSE 或 WebSocket

### 5. 后端业务 API 开发

适用于：Controller、Service、Mapper、Repository、DTO、VO、SQL、接口、业务逻辑。

读取：

- `04-backend-rules.md`
- `06-database-rules.md`
- `08-error-handling-rules.md`
- `09-testing-rules.md`
- `02-api-contract.md`
- `../architecture/agent-harness/10-web-service-bff.md`（涉及 Web 服务时读取）
- `../architecture/agent-harness/12-api-design.md`（涉及 Agent API 时读取）

开发前必须确认：

- API 路径和方法
- 请求参数
- 响应结构
- 参数校验
- 权限校验
- 事务边界
- 数据库影响
- 异常处理
- 测试方式

### 6. API 契约变更

适用于：接口路径、请求字段、响应字段、分页结构、错误码、枚举值、权限规则变化。

读取：

- `02-api-contract.md`
- `../architecture/agent-harness/12-api-design.md`
- `03-frontend-rules.md`
- `04-backend-rules.md`
- `10-review-checklist.md`

要求：

- 先改接口契约，再改前后端代码。
- 所有字段命名必须统一。
- 不允许前端和后端各自维护一份接口说明。
- 如果架构 API 设计和当前接口契约不同，先报告差异，再更新契约。

### 7. 安全 / 审批 / 审计开发

适用于：高风险工具审批、操作审计、权限校验、日志追踪、敏感操作拦截。

读取：

- `../architecture/agent-harness/09-security-approval-audit.md`
- `07-permission-rules.md`
- `08-error-handling-rules.md`
- `04-backend-rules.md`
- `10-review-checklist.md`

要求：

- 明确风险等级。
- 明确是否需要人工审批。
- 明确审计日志字段。
- 明确失败后的用户可见提示。

### 8. Bug 修复

读取：

- 与 bug 相关的源代码文件
- 相关任务类型对应规范
- `10-review-checklist.md`

要求：

- 先复现或定位原因。
- 只修 bug，不顺手重构无关代码。
- 修复后给出验证步骤。

### 9. 代码 Review

读取：

- `10-review-checklist.md`
- 变更文件对应的前端或后端规范
- `02-api-contract.md`
- 涉及 Agent 架构时读取对应 `../architecture/agent-harness/` 文档

要求：

- 不直接修改代码，除非用户明确要求。
- 输出问题、风险、建议修复方式。