# 管理员权限

管理员端点（`/api/admin/*`）调用 `requireAdmin()`，同时验证:
1. 请求携带有效的 Auth Session Token
2. 用户角色为 `admin`

不满足条件时:
- 无 token 或无有效 token 返回 **401**
- 非 admin 角色返回 **403**
