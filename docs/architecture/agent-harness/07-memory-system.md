# 07. Memory System 记忆系统设计

## 1. Memory 定位

Memory System 用来让 Agent 从历史任务中积累经验。

它不是简单的向量库，而是分层结构化记忆系统。

## 2. 记忆类型

| 类型 | 说明 |
|---|---|
| profile_memory | 用户长期偏好 |
| task_memory | 历史任务摘要 |
| procedure_memory | 成功流程经验 |
| failure_memory | 失败经验和避坑记录 |
| entity_memory | 项目、人、系统、业务对象 |
| project_memory | 项目级上下文 |

## 3. Memory 结构

```json
{
  "memory_id": "mem_001",
  "user_id": "u001",
  "memory_type": "procedure_memory",
  "content": "生成像素精灵图时，用户偏好 2x2 等分布局、#FF00FF 背景、脚锚固定、低幅动作。",
  "tags": ["pixel_art", "sprite_sheet", "game_asset"],
  "confidence": 0.92,
  "source_task_id": "task_001",
  "created_at": "2026-07-08T10:00:00",
  "updated_at": "2026-07-08T10:00:00"
}
```

## 4. 写入规则

应该写入：

- 用户长期偏好
- 项目固定配置
- 成功任务流程
- 失败教训
- 高频业务规则
- 工具调用经验
- 用户明确要求记住的信息

不应该写入：

- 一次性闲聊
- 临时任务参数
- 不确定结论
- 无意义碎片
- 高敏感隐私信息

## 5. Memory Writer 输出

```json
{
  "should_write_memory": true,
  "memories": [
    {
      "memory_type": "procedure_memory",
      "content": "用户偏好开发文档使用 Markdown，并按模块拆分。",
      "tags": ["documentation", "agent_harness"],
      "confidence": 0.9
    }
  ]
}
```

## 6. 检索流程

```text
当前任务
  ↓
生成 memory query
  ↓
按 user_id / project_id / tags 过滤
  ↓
向量检索 + 关键词检索
  ↓
rerank
  ↓
去重和压缩
  ↓
注入 Context Builder
```

## 7. Memory 使用策略

不同任务使用不同记忆：

| 任务类型 | 优先记忆 |
|---|---|
| 写文档 | 用户格式偏好、项目上下文、历史文档风格 |
| 调工具 | 业务实体、成功流程、失败经验 |
| 代码分析 | 项目路径、架构约定、历史 bug |
| 数据查询 | 指标口径、表结构、SQL 经验 |
| 图像素材 | 风格偏好、尺寸规范、背景色要求 |

## 8. Memory 质量控制

记忆必须有置信度。

低置信度记忆不直接影响执行，只作为参考。

过期机制：

- 用户偏好：长期有效
- 项目配置：直到被覆盖
- 任务经验：按命中率调整
- 失败记忆：如果连续成功可降权

## 9. Memory 与 Skill 的关系

Memory 是经验，Skill 是流程。

当某类 procedure_memory 多次命中且成功率高，可以转化为 Skill。