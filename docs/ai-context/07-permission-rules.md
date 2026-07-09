# Permission Rules

本文件定义权限相关规范。

## 基本原则

- 后端必须做真实权限校验。
- 前端权限控制只负责体验，不负责安全边界。
- 所有权限点必须命名清晰。
- 按钮权限和接口权限需要保持一致。

## 权限点命名建议

```text
module:resource:action
```

示例：

```text
system:user:list
system:user:create
system:user:update
system:user:delete
system:user:disable
```

## 前端权限规则

- 无权限按钮默认隐藏。
- 如果业务要求可见但不可点，需要禁用并展示提示。
- 页面级无权限时展示无权限状态。

## 后端权限规则

- 每个需要保护的接口必须检查权限。
- 不允许只依赖前端隐藏按钮。
- 批量操作需要检查每条数据的操作权限，按项目实际要求处理。

## API 契约要求

涉及权限的接口必须在 `02-api-contract.md` 中写明：

- Auth 是否 required
- Permission 权限点
- 无权限时的错误码和 message