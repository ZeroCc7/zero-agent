# 06. Skill Runtime 技能运行时设计

## 1. Skill 定位

Skill 是可复用的业务流程，由多个步骤组成。

相比 Tool，Skill 更接近业务能力。

示例：

- 知识库问答
- 数据库问数
- 摄像头实时预览 JSON 生成
- 风险研判报告生成
- 代码库分析
- 技术文档生成

## 2. Skill 定义

```yaml
skill_id: risk_assessment_report
name: 风险研判报告生成
version: 1.0.0
description: 根据事件标签和人员标签生成风险研判报告
input_schema:
  type: object
  properties:
    event_id:
      type: string
  required:
    - event_id
steps:
  - step_id: query_event_tags
    name: 查询事件标签
    type: tool
    tool_id: query_event_tags
    input:
      event_id: "${input.event_id}"
  - step_id: query_person_tags
    name: 查询人员标签
    type: tool
    tool_id: query_person_tags
    input:
      event_id: "${input.event_id}"
  - step_id: calculate_score
    name: 计算风险分数
    type: tool
    tool_id: calculate_risk_score
    input:
      event_tags: "${query_event_tags.output}"
      person_tags: "${query_person_tags.output}"
  - step_id: generate_report
    name: 生成研判报告
    type: llm
    input:
      risk_result: "${calculate_score.output}"
```

## 3. Skill Runtime 执行流程

```text
接收 skill_id + input
  ↓
加载 skill_definition
  ↓
校验 input_schema
  ↓
创建 skill_run
  ↓
按 steps 顺序执行
  ↓
每步复用 Executor 机制
  ↓
保存每步输入输出
  ↓
返回 skill output
```

## 4. Skill Step 类型

| 类型 | 说明 |
|---|---|
| tool | 调用工具 |
| llm | 调用模型生成、判断、总结 |
| template | 模板渲染 |
| condition | 条件判断 |
| skill | 调用子技能 |
| approval | 等待审批 |
| code | 沙箱代码执行 |

## 5. Skill 版本管理

Skill 需要版本化。

字段：

- skill_id
- version
- status: draft / published / deprecated
- created_by
- published_at
- changelog

生产环境只允许执行 published 版本。

## 6. Skill 成功率评估

记录：

- 总执行次数
- 成功次数
- 失败次数
- 平均耗时
- 人工修正次数
- 用户满意度
- 最近失败原因

## 7. 推荐首批内置 Skill

### 7.1 知识库问答

```text
用户问题 → Query Rewrite → 检索 → Rerank → 构建上下文 → 生成回答 → 引用来源
```

### 7.2 数据库问数

```text
用户问题 → 指标识别 → SQL 生成 → SQL 安全检查 → 查询 → 解释结果
```

### 7.3 摄像头调用

```text
用户描述 → 识别地点 → 匹配摄像头 → 生成 realplay_list JSON
```

### 7.4 代码库分析

```text
用户问题 → 调用 CodeGraph → 获取相关代码 → 分析调用链 → 输出修改建议
```

### 7.5 文档生成

```text
用户需求 → 大纲 → 内容生成 → 格式化 → 可选导出 Word/PDF
```