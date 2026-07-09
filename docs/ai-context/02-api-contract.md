# API Contract

本文件是前后端共享的唯一接口契约。所有 API 相关开发必须以本文件为准。

> 使用方式：每次新增或修改接口，先更新本文件，再让前端和后端分别实现。

## 全局约定

### Base URL

```text
/api
```

### 统一响应格式

```json
{
  "code": 200,
  "message": "success",
  "data": {}
}
```

### 分页响应格式

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "total": 0,
    "pageNum": 1,
    "pageSize": 10,
    "list": []
  }
}
```

### 分页请求参数

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| pageNum | number | 是 | 当前页码，从 1 开始 |
| pageSize | number | 是 | 每页数量 |

### 时间格式

```text
YYYY-MM-DD HH:mm:ss
```

### 常用状态枚举

| 枚举 | 说明 |
|---|---|
| enabled | 启用 |
| disabled | 禁用 |

### 常用错误码

| code | 说明 | 前端处理 |
|---:|---|---|
| 200 | 成功 | 正常处理 |
| 400 | 参数错误 | 展示错误信息 |
| 401 | 未登录 | 跳转登录或触发重新登录 |
| 403 | 无权限 | 展示无权限提示 |
| 404 | 资源不存在 | 展示错误信息 |
| 500 | 系统异常 | 展示统一异常提示 |


## Agent Harness API 落地说明

架构设计中的 API 参考文档位于：

- `../architecture/agent-harness/10-web-service-bff.md`
- `../architecture/agent-harness/12-api-design.md`

本文件是前后端执行层面的唯一契约。凡是架构文档中提到但本文件未定义的接口，开发前必须先补充到本文件。

### Agent 常用资源命名

| 资源 | 建议路径前缀 | 说明 |
|---|---|---|
| 会话 | `/api/agent/conversations` | 管理用户与 Agent 的会话 |
| 任务 | `/api/agent/tasks` | 创建、查询、取消 Agent 任务 |
| 运行记录 | `/api/agent/runs` | 查询 Agent 执行过程和状态 |
| 工具 | `/api/agent/tools` | 查询工具注册、工具调用结果 |
| 技能 | `/api/agent/skills` | 查询和执行已注册技能 |
| 审批 | `/api/agent/approvals` | 高风险操作审批 |
| 审计 | `/api/agent/audits` | 操作和工具调用审计 |
| 记忆 | `/api/agent/memories` | 用户/项目/任务记忆管理 |

### Agent 任务状态枚举

| 状态 | 说明 |
|---|---|
| pending | 已创建，等待执行 |
| planning | 正在规划 |
| running | 正在执行 |
| waiting_approval | 等待人工审批 |
| paused | 已暂停 |
| succeeded | 执行成功 |
| failed | 执行失败 |
| canceled | 已取消 |

### 流式输出约定

长任务可以使用 SSE 或 WebSocket。落地前必须在具体接口中明确：

- 连接地址
- 事件类型
- 事件字段
- 断线重连策略
- 最终完成事件
- 错误事件

建议 SSE 事件类型：

| event | 说明 |
|---|---|
| `message` | 普通模型输出 |
| `thinking` | 可公开的阶段性思考摘要 |
| `tool_call` | 工具调用开始 |
| `tool_result` | 工具调用结果 |
| `approval_required` | 需要人工审批 |
| `task_status` | 任务状态变化 |
| `done` | 任务完成 |
| `error` | 任务失败或流异常 |

## API 模板

新增接口时复制以下模板。

```md
## 接口名称

### 基本信息

- Method: `GET | POST | PUT | DELETE`
- Path: `/api/example`
- Auth: required / optional
- Permission: `example:read`

### 业务说明

说明接口用途、业务规则、边界情况。

### 请求参数

| 字段 | 位置 | 类型 | 必填 | 说明 |
|---|---|---|---:|---|
| pageNum | query | number | 是 | 当前页码 |

### 请求示例

```json
{}
```

### 响应字段

| 字段 | 类型 | 说明 |
|---|---|---|
| id | string | 主键 ID |

### 响应示例

```json
{
  "code": 200,
  "message": "success",
  "data": {}
}
```

### 错误情况

| code | 场景 | message 示例 |
|---:|---|---|
| 400 | 参数错误 | 参数不能为空 |

### 前端处理规则

- loading 状态：请求中显示加载状态。
- empty 状态：列表为空时显示空状态。
- error 状态：接口异常时显示错误提示。
- permission 状态：无权限时隐藏对应按钮或展示无权限提示。
```