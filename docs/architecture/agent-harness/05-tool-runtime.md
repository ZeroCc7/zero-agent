# 05. Tool Runtime 设计

## 1. Tool Runtime 定位

Tool Runtime 负责管理和执行 Agent 可调用的原子能力。

工具可以是：

- HTTP API
- 数据库查询
- MCP 工具
- Python 函数
- 文件操作
- 搜索服务
- 知识库检索
- 业务系统接口

## 2. Tool Registry

Tool Registry 存储所有工具的定义。

工具定义示例：

```yaml
tool_id: query_event_tags
name: 查询事件标签
description: 根据 event_id 查询事件相关标签
category: database
risk_level: medium
requires_approval: false
timeout_seconds: 20
input_schema:
  type: object
  properties:
    event_id:
      type: string
  required:
    - event_id
output_schema:
  type: object
endpoint_config:
  type: http
  method: POST
  url: http://risk-service/api/event/tags
```

## 3. 风险等级

| 风险等级 | 示例 | 策略 |
|---|---|---|
| low | 搜索、格式转换、读取公开资料 | 自动执行 |
| medium | 查询内部数据、读取邮件、读文件 | 自动执行 + 审计 |
| high | 发邮件、写数据库、改日程 | 用户确认 |
| critical | 删除数据、生产发布、权限变更 | 强审批 + 审计 + 可回滚优先 |

## 4. 工具执行流程

```text
Executor 请求执行工具
  ↓
读取 Tool Definition
  ↓
检查 Agent 是否有权限
  ↓
校验 input_schema
  ↓
判断是否需要审批
  ↓
执行工具
  ↓
校验 output_schema
  ↓
写入 audit_log
  ↓
返回结果
```

## 5. HTTP Tool

```python
class HttpToolExecutor:
    async def execute(self, tool_def, input_data):
        endpoint = tool_def.endpoint_config
        response = await http_client.request(
            method=endpoint["method"],
            url=endpoint["url"],
            json=input_data,
            timeout=tool_def.timeout_seconds,
        )
        response.raise_for_status()
        return response.json()
```

## 6. Database Tool

数据库工具必须明确读写权限。

```yaml
tool_id: database_query
category: database
mode: read_only
requires_approval: false
```

写操作必须审批。

SQL 安全要求：

- 禁止多语句执行
- 默认只允许 SELECT
- 禁止 DROP / DELETE / UPDATE / INSERT，除非工具被明确注册为写工具
- 限制最大返回行数
- 记录完整 SQL 与参数

## 7. MCP Tool

```python
class McpToolExecutor:
    async def execute(self, server_name, tool_name, arguments):
        client = await mcp_pool.get_client(server_name)
        return await client.call_tool(tool_name, arguments)
```

## 8. 工具错误处理

工具错误统一返回：

```json
{
  "success": false,
  "error_type": "timeout | validation_error | permission_denied | remote_error",
  "message": "工具调用超时",
  "retryable": true
}
```

## 9. 工具调用审计

每次工具调用记录：

- task_id
- step_id
- tool_id
- input
- output
- status
- latency_ms
- error_message
- caller_agent
- user_id