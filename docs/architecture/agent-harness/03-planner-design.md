# 03. Planner 任务规划器设计

## 1. Planner 定位

Planner 负责将用户目标拆解成结构化执行计划。

它不执行工具，不直接访问数据库，不直接改状态，只输出计划。

## 2. Planner 输入

```json
{
  "user_message": "帮我分析这个事件的风险等级",
  "intent_type": "workflow",
  "available_tools": [
    "query_event_tags",
    "query_person_tags",
    "calculate_risk_score"
  ],
  "available_skills": [
    "risk_assessment_report"
  ],
  "session_context": {},
  "user_memory": []
}
```

## 3. Planner 输出

```json
{
  "task_type": "risk_assessment",
  "risk_level": "medium",
  "need_clarification": false,
  "clarification_question": null,
  "requires_user_approval": false,
  "plan": [
    {
      "step_id": "step_1",
      "name": "查询事件标签",
      "type": "tool",
      "tool_id": "query_event_tags",
      "input": {
        "event_id": "${event_id}"
      }
    },
    {
      "step_id": "step_2",
      "name": "查询人员标签",
      "type": "tool",
      "tool_id": "query_person_tags",
      "input": {
        "event_id": "${event_id}"
      }
    },
    {
      "step_id": "step_3",
      "name": "计算风险分数",
      "type": "tool",
      "tool_id": "calculate_risk_score",
      "input": {
        "event_tags": "${step_1.output}",
        "person_tags": "${step_2.output}"
      }
    }
  ]
}
```

## 4. Step 类型

| 类型 | 说明 |
|---|---|
| `llm` | 调用大模型生成、判断、总结 |
| `tool` | 调用单个工具 |
| `skill` | 调用一个技能流程 |
| `condition` | 条件判断 |
| `approval` | 人工审批 |
| `template` | 模板渲染 |
| `code` | 沙箱代码执行 |

## 5. Planner 规则

1. 必须输出 JSON。
2. 不允许输出自然语言计划后让系统猜。
3. 不允许选择未授权工具。
4. 对高风险操作必须标记 `requires_user_approval=true`。
5. 信息不足时必须输出澄清问题。
6. 每个 step 必须有清晰输入来源。

## 6. Planner Prompt 合约

```text
你是任务规划器。你的职责是将用户目标拆解为结构化执行计划。
你不能直接执行工具。
你只能从可用工具和技能列表中选择能力。
你必须只输出合法 JSON，不要输出 Markdown。
```

输出 Schema：

```json
{
  "task_type": "string",
  "risk_level": "low | medium | high | critical",
  "need_clarification": "boolean",
  "clarification_question": "string | null",
  "requires_user_approval": "boolean",
  "plan": [
    {
      "step_id": "string",
      "name": "string",
      "type": "llm | tool | skill | condition | approval | template | code",
      "tool_id": "string | null",
      "skill_id": "string | null",
      "input": {}
    }
  ]
}
```

## 7. Replan 机制

Verifier 或 Executor 发现失败时，可以触发重新规划。

常见触发条件：

- 工具返回为空
- 参数缺失
- 输出格式错误
- 用户目标未完成
- 安全检查失败
- 某一步超时

Replan 输入：

```json
{
  "original_plan": {},
  "failed_step": "step_2",
  "error_message": "人员标签查询为空",
  "tool_outputs": {},
  "user_goal": "分析风险等级"
}
```

Replan 输出新的计划或补救步骤。