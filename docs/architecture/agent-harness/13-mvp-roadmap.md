# 13. MVP 开发路线和任务拆解

## 1. MVP 目标

第一版不要追求复杂多 Agent，而是先打通一个稳定闭环：

```text
用户请求
  ↓
创建任务
  ↓
Router 识别意图
  ↓
Planner 生成计划
  ↓
Executor 执行 step
  ↓
Tool 调用
  ↓
Verifier 检查
  ↓
Audit 记录
  ↓
返回结果
```

## 2. 阶段一：基础 Runtime

目标：让 Agent 可以接收请求、生成计划、返回结果。

任务：

- [ ] 创建 FastAPI 项目
- [ ] 实现 `/api/agent/chat`
- [ ] 实现 Task 创建
- [ ] 实现 Router
- [ ] 实现 Planner JSON 输出
- [ ] 实现基础 Executor
- [ ] 接入一个 LLM Client
- [ ] 实现 audit_log 写入

交付标准：

- 输入一个复杂请求，系统能生成结构化计划
- 能执行 LLM step
- 能记录任务日志

## 3. 阶段二：Tool Runtime

目标：支持工具注册和调用。

任务：

- [ ] 实现 tool_definition 表
- [ ] 实现 Tool Registry
- [ ] 实现 HTTP Tool Executor
- [ ] 实现 Tool input_schema 校验
- [ ] 实现 Tool output_schema 校验
- [ ] 实现工具调用日志
- [ ] 实现工具超时和重试

交付标准：

- Planner 能选择工具
- Executor 能调用工具
- 工具输入输出可审计

## 4. 阶段三：Task 状态机

目标：让任务可追踪、可恢复。

任务：

- [ ] 实现 agent_task 表
- [ ] 实现 agent_task_step 表
- [ ] 实现状态流转
- [ ] 实现步骤执行记录
- [ ] 实现任务查询 API
- [ ] 实现步骤查询 API
- [ ] 实现失败重试

交付标准：

- 前端可以看到任务状态
- 可以查看每一步输入输出
- 失败任务可以定位原因

## 5. 阶段四：Skill Runtime

目标：沉淀可复用业务流程。

任务：

- [ ] 实现 skill_definition 表
- [ ] 实现 Skill Loader
- [ ] 实现 Skill Step 执行
- [ ] 支持 tool / llm / template step
- [ ] 实现 Skill 测试接口
- [ ] 实现 Skill 版本状态

交付标准：

- 可以配置一个风险研判 Skill
- 可以配置一个知识库问答 Skill
- 可以配置一个摄像头调用 Skill

## 6. 阶段五：Web Service / BFF

目标：让前端可接入。

任务：

- [ ] 实现 `/api/agent/chat/stream`
- [ ] 实现 SSE 事件推送
- [ ] 实现 Agent 管理 API
- [ ] 实现 Tool 管理 API
- [ ] 实现 Skill 管理 API
- [ ] 实现任务追踪 API

交付标准：

- 前端可以发起对话
- 前端可以看到执行过程
- 前端可以管理 Agent / Tool / Skill

## 7. 阶段六：审批与安全

目标：控制高风险操作。

任务：

- [ ] 实现工具风险等级
- [ ] 实现权限检查
- [ ] 实现 approval_record 表
- [ ] 实现审批 API
- [ ] 实现 waiting_approval 状态
- [ ] 实现数据脱敏
- [ ] 实现 Prompt Injection 基础防护

交付标准：

- 高风险工具不会自动执行
- 用户确认后才继续任务
- 审批行为可审计

## 8. 阶段七：Memory System

目标：让 Agent 逐步变聪明。

任务：

- [ ] 实现 agent_memory 表
- [ ] 实现 Memory Writer
- [ ] 实现 Memory Retriever
- [ ] 支持用户偏好记忆
- [ ] 支持流程经验记忆
- [ ] 接入 embedding / pgvector

交付标准：

- 下一次同类任务可以利用历史经验
- 用户偏好可被检索并影响输出

## 9. 推荐首批 Demo 场景

### 9.1 摄像头调用

```text
用户：把大门口的视频调出来
Agent：询问具体摄像头
用户：大门A
Agent：输出 realplay_list JSON
```

### 9.2 风险研判

```text
用户：分析事件 E001 的风险等级
Agent：查询事件标签、人员标签、计算分数、生成结论
```

### 9.3 知识库问答

```text
用户：这个政策文件里关于审批流程怎么说？
Agent：检索知识库、引用来源、生成回答
```

### 9.4 CodeGraph 代码分析

```text
用户：这个接口 500 是哪里导致的？
Agent：调用 CodeGraph 找调用链、定位异常、给修改建议
```

## 10. 第一版技术选型

| 模块 | 建议 |
|---|---|
| Runtime | Python + FastAPI |
| BFF | Spring Boot 或 FastAPI |
| 数据库 | PostgreSQL |
| 缓存 | Redis |
| 队列 | Celery / Dramatiq / Arq |
| 向量库 | pgvector |
| 前端 | Vue 3 |
| 日志 | PostgreSQL + OpenTelemetry |

## 11. 第一版不要做的事

- 不要一开始做复杂多 Agent 协作
- 不要一开始做可视化 DAG 编排器
- 不要让模型直接写生产库
- 不要把所有工具 schema 全塞进上下文
- 不要跳过审计日志
- 不要跳过任务状态机

第一版重点是稳定闭环，不是炫技。