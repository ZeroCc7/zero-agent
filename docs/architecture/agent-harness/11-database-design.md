# 11. 数据库设计

推荐使用 PostgreSQL。

## 1. agent_definition

```sql
CREATE TABLE agent_definition (
    id BIGSERIAL PRIMARY KEY,
    agent_id VARCHAR(128) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    system_prompt TEXT,
    status VARCHAR(32) DEFAULT 'enabled',
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);
```

## 2. tool_definition

```sql
CREATE TABLE tool_definition (
    id BIGSERIAL PRIMARY KEY,
    tool_id VARCHAR(128) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    category VARCHAR(64),
    risk_level VARCHAR(32),
    requires_approval BOOLEAN DEFAULT FALSE,
    input_schema JSONB,
    output_schema JSONB,
    endpoint_config JSONB,
    timeout_seconds INT DEFAULT 30,
    status VARCHAR(32) DEFAULT 'enabled',
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);
```

## 3. skill_definition

```sql
CREATE TABLE skill_definition (
    id BIGSERIAL PRIMARY KEY,
    skill_id VARCHAR(128) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    version VARCHAR(32),
    input_schema JSONB,
    output_schema JSONB,
    steps JSONB,
    status VARCHAR(32) DEFAULT 'draft',
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);
```

## 4. agent_task

```sql
CREATE TABLE agent_task (
    id BIGSERIAL PRIMARY KEY,
    task_id VARCHAR(128) UNIQUE NOT NULL,
    session_id VARCHAR(128),
    user_id VARCHAR(128),
    agent_id VARCHAR(128),
    user_input TEXT,
    intent_type VARCHAR(64),
    status VARCHAR(32),
    risk_level VARCHAR(32),
    plan JSONB,
    current_step_id VARCHAR(128),
    final_output TEXT,
    error_message TEXT,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);
```

## 5. agent_task_step

```sql
CREATE TABLE agent_task_step (
    id BIGSERIAL PRIMARY KEY,
    task_id VARCHAR(128) NOT NULL,
    step_id VARCHAR(128) NOT NULL,
    step_name VARCHAR(255),
    step_type VARCHAR(64),
    status VARCHAR(32),
    input JSONB,
    output JSONB,
    error_message TEXT,
    started_at TIMESTAMP,
    finished_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW()
);
```

## 6. agent_memory

如果使用 pgvector：

```sql
CREATE EXTENSION IF NOT EXISTS vector;

CREATE TABLE agent_memory (
    id BIGSERIAL PRIMARY KEY,
    memory_id VARCHAR(128) UNIQUE NOT NULL,
    user_id VARCHAR(128),
    project_id VARCHAR(128),
    memory_type VARCHAR(64),
    content TEXT,
    tags TEXT[],
    embedding VECTOR(1024),
    confidence NUMERIC(5, 4),
    source_task_id VARCHAR(128),
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);
```

如果第一版不接向量库，可以先移除 embedding 字段。

## 7. approval_record

```sql
CREATE TABLE approval_record (
    id BIGSERIAL PRIMARY KEY,
    approval_id VARCHAR(128) UNIQUE NOT NULL,
    task_id VARCHAR(128),
    step_id VARCHAR(128),
    action_name VARCHAR(255),
    risk_level VARCHAR(32),
    summary TEXT,
    payload JSONB,
    status VARCHAR(32),
    approved_by VARCHAR(128),
    approved_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW()
);
```

## 8. audit_log

```sql
CREATE TABLE audit_log (
    id BIGSERIAL PRIMARY KEY,
    audit_id VARCHAR(128) UNIQUE NOT NULL,
    task_id VARCHAR(128),
    user_id VARCHAR(128),
    event_type VARCHAR(64),
    event_name VARCHAR(255),
    input JSONB,
    output JSONB,
    status VARCHAR(32),
    latency_ms INT,
    error_message TEXT,
    created_at TIMESTAMP DEFAULT NOW()
);
```

## 9. 推荐索引

```sql
CREATE INDEX idx_agent_task_user_id ON agent_task(user_id);
CREATE INDEX idx_agent_task_status ON agent_task(status);
CREATE INDEX idx_agent_task_session_id ON agent_task(session_id);
CREATE INDEX idx_task_step_task_id ON agent_task_step(task_id);
CREATE INDEX idx_audit_task_id ON audit_log(task_id);
CREATE INDEX idx_memory_user_id ON agent_memory(user_id);
CREATE INDEX idx_memory_type ON agent_memory(memory_type);
CREATE INDEX idx_approval_status ON approval_record(status);
```