# 10. Agent Web Service / BFF 设计

## 1. 定位

Agent Web Service 是前端唯一调用入口。

前端不直接调用：

- 大模型
- Tool Runtime
- Skill Runtime
- 数据库
- MCP Server
- 内部业务服务

所有能力都必须经过 Web Service 统一鉴权、权限控制、任务管理和审计。

## 2. 架构位置

```text
Frontend Web
  ↓ HTTP / SSE / WebSocket
Agent Web Service / BFF
  ↓ 内部 HTTP / RPC / 函数调用
Agent Runtime
  ↓
Tool / Skill / Memory / Business Services
```

## 3. Web Service 职责

| 职责 | 说明 |
|---|---|
| 用户鉴权 | 校验 Token、租户、角色 |
| 会话管理 | 管理 session |
| 任务创建 | 用户请求生成 task |
| 流式输出 | SSE / WebSocket 推送模型输出和步骤状态 |
| 任务查询 | 查询 task、steps、日志 |
| 审批处理 | 接收用户同意 / 拒绝 |
| 管理配置 | Agent / Tool / Skill CRUD |
| 错误封装 | 返回统一错误格式 |

## 4. 推荐服务拆分

企业级推荐：

```text
Vue / React
  ↓
Spring Boot BFF
  ↓
Python Agent Runtime
```

快速原型推荐：

```text
Vue / React
  ↓
FastAPI Agent Web Service
  ↓
Python Agent Runtime
```

## 5. 流式接口

```http
POST /api/agent/chat/stream
```

SSE 示例：

```text
event: task_created
data: {"task_id":"task_001","status":"planning"}

event: step_started
data: {"step_id":"step_1","name":"任务规划"}

event: delta
data: {"content":"正在分析你的需求..."}

event: step_finished
data: {"step_id":"step_1","status":"success"}

event: done
data: {"task_id":"task_001","status":"completed"}
```

## 6. 前端页面

### 6.1 Agent 对话页

```text
左侧：会话列表
中间：对话内容
右侧：任务执行 Timeline
底部：输入框
```

### 6.2 任务追踪页

- 任务列表
- 状态筛选
- 步骤详情
- 错误详情
- 重试 / 终止

### 6.3 Tool 管理页

- 工具列表
- Schema 编辑
- 风险等级配置
- 测试调用
- 调用日志

### 6.4 Skill 编排页

- Skill 列表
- 步骤配置
- 版本管理
- 发布 / 回滚
- 测试运行

### 6.5 审批中心

- 待审批列表
- 操作摘要
- Payload 查看
- 同意 / 拒绝
- 审批历史

## 7. 统一返回格式

```json
{
  "code": 0,
  "message": "success",
  "data": {}
}
```

错误示例：

```json
{
  "code": 50001,
  "message": "工具调用失败",
  "data": {
    "task_id": "task_001",
    "step_id": "step_2",
    "error": "connection timeout"
  }
}
```