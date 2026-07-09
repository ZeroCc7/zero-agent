# docs 目录说明

## architecture/agent-harness

系统架构文档，包含 Agent Runtime、Planner、Context Builder、Tool Runtime、Skill Runtime、Memory、Task 状态机、安全审计、Web Service / BFF、数据库、API 和 MVP 路线。

## ai-context

opencode / AI Coding Agent 开发规范文档，包含文档路由、项目总览、API 契约、前端规范、后端规范、UI 交互、数据库、权限、错误处理、测试和 Review 清单。

## 使用建议

- 做系统设计、模块拆分、服务边界、运行时机制时，看 `architecture/agent-harness`。
- 做具体编码任务、前后端开发、接口联调、Review 时，看 `ai-context`。
- 不要把两个目录混成一份超长文档，否则 Agent 上下文会变脏。