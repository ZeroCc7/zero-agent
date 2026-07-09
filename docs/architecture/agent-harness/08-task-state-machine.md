# 08. Task 状态机与执行流

## 1. Task 定位

Task 是一次用户请求对应的执行实例。

每个 Task 包含：

- 用户输入
- Agent 信息
- 意图识别结果
- 执行计划
- 当前步骤
- 执行状态
- 步骤输入输出
- 错误信息
- 最终结果

## 2. Task 状态

```text
created
  ↓
planning
  ↓
running
  ↓
verifying
  ↓
completed
```

异常状态：

```text
waiting_approval
paused
failed
cancelled
timeout
```

## 3. Step 状态

```text
pending
running
success
failed
skipped
waiting_approval
```

## 4. 状态流转图

```text
created → planning
planning → running
planning → waiting_approval
running → verifying
running → waiting_approval
running → failed
verifying → completed
verifying → planning
waiting_approval → running
waiting_approval → cancelled
```

## 5. Executor 主循环

```python
class TaskExecutor:
    async def execute(self, task):
        while not task.is_finished():
            step = task.current_step()
            step.status = "running"

            try:
                await self.before_step(task, step)
                result = await self.execute_step(task, step)
                await self.after_step(task, step, result)
                task.move_next()
            except ApprovalRequired:
                task.status = "waiting_approval"
                break
            except Exception as e:
                if await self.should_retry(task, step, e):
                    continue
                task.status = "failed"
                task.error_message = str(e)
                break

        return task
```

## 6. Step 执行前检查

执行每一步前必须检查：

- 工具是否存在
- Agent 是否有权限
- 参数是否完整
- input_schema 是否通过
- 是否需要审批
- 是否超过最大重试次数

## 7. 变量引用规则

Plan 中允许使用变量引用：

```text
${input.event_id}
${step_1.output}
${query_event_tags.output.tags}
${memory.user_preference}
```

变量解析失败时，step 应失败并触发 replan 或 ask_user。

## 8. 重试策略

可重试错误：

- 网络超时
- 临时服务不可用
- 模型 JSON 格式错误
- 工具限流

不可重试错误：

- 权限不足
- 参数缺失且无法推断
- 用户拒绝审批
- 安全策略拦截

## 9. 任务恢复

长任务需要支持从 `current_step_id` 恢复。

恢复时：

1. 读取 task
2. 读取已完成 step output
3. 从当前 step 继续
4. 保证已执行的高风险操作不重复执行

幂等性要求：

- 每个 step 应有 run_id
- 外部写操作应带 idempotency_key
- 重试时不能重复发邮件、重复写库、重复创建日程