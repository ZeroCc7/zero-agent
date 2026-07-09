# 12. API 设计

## 1. Agent 对话

### 1.1 普通对话

```http
POST /api/agent/chat
```

请求：

```json
{
  "agent_id": "general_agent",
  "session_id": "session_001",
  "message": "帮我分析这个事件的风险等级",
  "stream": false
}
```

响应：

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "task_id": "task_001",
    "session_id": "session_001",
    "status": "completed",
    "answer": "该事件综合判断为较大风险..."
  }
}
```

### 1.2 流式对话

```http
POST /api/agent/chat/stream
```

SSE 事件：

- task_created
- step_started
- delta
- step_finished
- approval_required
- error
- done

## 2. 任务查询

### 2.1 查询任务详情

```http
GET /api/agent/tasks/{task_id}
```

### 2.2 查询任务步骤

```http
GET /api/agent/tasks/{task_id}/steps
```

### 2.3 查询步骤详情

```http
GET /api/agent/tasks/{task_id}/steps/{step_id}
```

### 2.4 取消任务

```http
POST /api/agent/tasks/{task_id}/cancel
```

### 2.5 重试任务

```http
POST /api/agent/tasks/{task_id}/retry
```

## 3. 审批接口

### 3.1 查询待审批

```http
GET /api/agent/approvals/pending
```

### 3.2 同意审批

```http
POST /api/agent/approvals/{approval_id}/approve
```

请求：

```json
{
  "comment": "确认执行"
}
```

### 3.3 拒绝审批

```http
POST /api/agent/approvals/{approval_id}/reject
```

请求：

```json
{
  "comment": "暂不执行"
}
```

## 4. Agent 管理

```http
GET    /api/agents
POST   /api/agents
GET    /api/agents/{agent_id}
PUT    /api/agents/{agent_id}
DELETE /api/agents/{agent_id}
```

## 5. Tool 管理

```http
GET  /api/tools
POST /api/tools
GET  /api/tools/{tool_id}
PUT  /api/tools/{tool_id}
POST /api/tools/{tool_id}/test
```

注册工具请求：

```json
{
  "tool_id": "query_event_tags",
  "name": "查询事件标签",
  "description": "根据 event_id 查询事件标签",
  "category": "database",
  "risk_level": "medium",
  "requires_approval": false,
  "input_schema": {},
  "output_schema": {},
  "endpoint_config": {}
}
```

## 6. Skill 管理

```http
GET  /api/skills
POST /api/skills
GET  /api/skills/{skill_id}
PUT  /api/skills/{skill_id}
POST /api/skills/{skill_id}/publish
POST /api/skills/{skill_id}/test
```

## 7. 审计日志

```http
GET /api/audit/tasks/{task_id}
GET /api/audit/tools/{tool_id}
GET /api/audit/users/{user_id}
```

## 8. 统一响应

```json
{
  "code": 0,
  "message": "success",
  "data": {}
}
```

错误码建议：

| code | 说明 |
|---|---|
| 0 | 成功 |
| 40001 | 参数错误 |
| 40101 | 未登录 |
| 40301 | 权限不足 |
| 40401 | 资源不存在 |
| 50001 | 工具调用失败 |
| 50002 | 模型调用失败 |
| 50003 | 任务执行失败 |
| 50004 | 审批被拒绝 |