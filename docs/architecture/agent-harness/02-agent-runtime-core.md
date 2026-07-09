# 02. Agent Runtime Core 核心设计

## 1. Runtime 职责

Agent Runtime 是整个系统的核心，负责把用户请求转化为可执行任务，并协调 Planner、Context Builder、Executor、Tool、Skill、Memory、Verifier 完成闭环。

Runtime 不直接等同于大模型调用层。它是 Agent 的执行操作系统。

## 2. 核心模块

```text
AgentRuntime
├── IntentRouter
├── Planner
├── ContextBuilder
├── Executor
├── ToolRuntime
├── SkillRuntime
├── Verifier
├── MemoryRetriever
├── MemoryWriter
├── ApprovalService
└── AuditService
```

## 3. 请求处理主流程

```text
1. 接收用户请求
2. 创建 task
3. 调用 Router 识别意图
4. 调用 Planner 生成计划
5. 保存 plan 到 task
6. Executor 按 step 执行
7. 每个 step 执行前做权限检查
8. 调用 Tool / Skill / LLM
9. 保存 step 输入输出
10. Verifier 检查最终结果
11. 必要时 Replan
12. 写入 Memory
13. 返回最终响应
```

## 4. Runtime 输入

```json
{
  "user_id": "u001",
  "session_id": "s001",
  "agent_id": "general_agent",
  "message": "帮我分析这个事件的风险等级",
  "attachments": [],
  "stream": true
}
```

## 5. Runtime 输出

```json
{
  "task_id": "task_001",
  "status": "completed",
  "answer": "该事件综合判断为较大风险，原因如下...",
  "steps": [],
  "audit_id": "audit_001"
}
```

## 6. Runtime 类设计示例

```python
class AgentRuntime:
    def __init__(
        self,
        router,
        planner,
        context_builder,
        executor,
        verifier,
        memory_writer,
        audit_service,
    ):
        self.router = router
        self.planner = planner
        self.context_builder = context_builder
        self.executor = executor
        self.verifier = verifier
        self.memory_writer = memory_writer
        self.audit_service = audit_service

    async def run(self, request):
        task = await self.create_task(request)

        intent = await self.router.route(task)
        task.intent = intent

        plan = await self.planner.plan(task, intent)
        task.plan = plan
        await self.save_task(task)

        result = await self.executor.execute(task)
        verify_result = await self.verifier.verify(task, result)

        if verify_result.action == "replan":
            new_plan = await self.planner.replan(task, verify_result)
            task.plan = new_plan
            result = await self.executor.execute(task)

        await self.memory_writer.write(task, result)
        await self.audit_service.record_task_finished(task, result)
        return result
```

## 7. Runtime 关键边界

### 7.1 Runtime 不负责业务逻辑细节

业务逻辑应沉淀到 Tool 或 Skill 中。

### 7.2 Runtime 不直接操作数据库业务表

应该通过 Tool 或业务服务 API 访问业务数据。

### 7.3 Runtime 不直接暴露给前端

前端通过 Agent Web Service / BFF 调用 Runtime。

## 8. 推荐目录结构

```text
agent-runtime/
├── app/
│   ├── core/
│   │   ├── runtime.py
│   │   ├── router.py
│   │   ├── planner.py
│   │   ├── context_builder.py
│   │   ├── executor.py
│   │   ├── verifier.py
│   │   └── memory_writer.py
│   ├── tools/
│   ├── skills/
│   ├── memory/
│   ├── security/
│   ├── models/
│   └── infra/
└── tests/
```