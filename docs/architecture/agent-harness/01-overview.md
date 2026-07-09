# 01. 总体架构设计

## 1. 项目定位

本项目定位为企业级 Agent Harness + Skill Runtime。

它不是简单封装一个大模型接口，而是为大模型提供一套完整运行环境，使 Agent 能够完成真实业务任务。

核心目标：

- 理解用户目标
- 拆解任务计划
- 调用工具和业务系统
- 管理任务状态
- 支持长期记忆
- 沉淀可复用技能
- 对高风险操作进行审批
- 对所有关键行为进行审计
- 支持后续持续优化和评估

## 2. 总体架构

```text
用户 / 前端 / API 调用方
   ↓
Agent Web Service / BFF
   ↓
Agent Runtime Core
   ↓
Router / Planner / Context Builder / Executor / Verifier
   ↓
Tool Runtime / Skill Runtime / Memory System
   ↓
业务系统 / 数据库 / 知识库 / MCP / 外部 API
```

## 3. 系统分层

```text
┌────────────────────────────────────────────┐
│ 1. 用户交互层                               │
│ Web / API / IM / Email / Workflow           │
├────────────────────────────────────────────┤
│ 2. Web Service / BFF 层                      │
│ 鉴权 / 会话 / SSE / 任务查询 / 审批 / 管理台 │
├────────────────────────────────────────────┤
│ 3. Agent Runtime 层                         │
│ Router / Planner / Executor / Verifier      │
├────────────────────────────────────────────┤
│ 4. 上下文工程层                              │
│ Context Builder / RAG / Memory Retriever    │
├────────────────────────────────────────────┤
│ 5. 能力层                                    │
│ Tool Registry / Skill Runtime / MCP Client  │
├────────────────────────────────────────────┤
│ 6. 状态与任务层                              │
│ Task State / Queue / Scheduler / Worker     │
├────────────────────────────────────────────┤
│ 7. 安全治理层                                │
│ Permission / Approval / Audit / Guardrails  │
├────────────────────────────────────────────┤
│ 8. 存储层                                    │
│ PostgreSQL / Redis / Vector DB / MinIO       │
└────────────────────────────────────────────┘
```

## 4. 核心概念

### 4.1 Agent

Agent 是具备固定职责、权限、工具集合和系统提示词的智能体实例。

示例：

```yaml
agent_id: gov_assistant
name: 政务业务助手
description: 面向政务业务场景的问答、数据查询和文档生成智能体
allowed_tools:
  - knowledge_search
  - database_read
  - document_generate
allowed_skills:
  - risk_assessment_report
  - camera_realplay_builder
```

### 4.2 Tool

Tool 是原子能力，一般对应一个 API、函数、MCP 工具、数据库查询或内部服务。

### 4.3 Skill

Skill 是由多个 Tool / LLM / Template / Condition 步骤组成的业务流程。

Tool 偏底层能力，Skill 偏业务能力。

### 4.4 Task

Task 是一次用户请求对应的任务实例，包含计划、步骤、状态、输出和审计记录。

### 4.5 Memory

Memory 是 Agent 的长期经验系统，用于保存用户偏好、成功流程、失败经验、业务实体和项目上下文。

## 5. 核心运行链路

```text
用户请求
  ↓
Intent Router：识别意图与任务类型
  ↓
Planner：生成结构化计划
  ↓
Context Builder：构建最小必要上下文
  ↓
Executor：执行计划步骤
  ↓
Tool / Skill Runtime：调用实际能力
  ↓
Verifier：检查结果是否满足目标
  ↓
Memory Writer：沉淀经验
  ↓
Final Response：返回最终结果
```

## 6. 设计原则

1. 不让模型直接操作系统，模型只给出结构化决策。
2. 所有工具调用必须经过权限检查和审计。
3. 高风险操作必须人工确认。
4. 上下文只注入当前任务需要的信息。
5. 高频任务沉淀为 Skill，不依赖每次临场推理。
6. 每次任务结束后进行复盘，形成长期记忆。