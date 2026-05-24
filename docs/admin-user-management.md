# 3.1 用户管理

#### GET /api/admin/users

搜索和分页列出用户。

**查询参数**:

| 参数 | 类型 | 必填 | 默认 | 说明 |
|------|------|------|------|------|
| search | string | 否 | 空 | 按用户名或邮箱模糊搜索 |
| page | number | 否 | 1 | 页码（每页 20 条） |

**响应 (200)**:

```json
{
  "users": [
    {
      "id": "clx...",
      "email": "alice@example.com",
      "username": "alice",
      "displayName": "Alice",
      "role": "user",
      "emailVerified": true,
      "createdAt": "2025-01-15T10:00:00.000Z"
    }
  ],
  "total": 1,
  "page": 1,
  "pageSize": 20
}
```

**curl 示例**:

```bash
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  "https://cpoauth.com/api/admin/users?search=alice&page=1"
```

---

#### PATCH /api/admin/users/:id

更新用户的角色或邮箱验证状态。

**请求体**:

| 参数 | 类型 | 说明 |
|------|------|------|
| role | string | `user` 或 `admin`（不能修改自己的角色） |
| emailVerified | boolean | 邮箱验证状态 |

**响应 (200)**:

```json
{
  "id": "clx...",
  "email": "alice@example.com",
  "username": "alice",
  "displayName": "Alice",
  "role": "admin",
  "emailVerified": true,
  "createdAt": "2025-01-15T10:00:00.000Z"
}
```

**错误码**: `400` 不能修改自己的角色

**curl 示例**:

```bash
curl -X PATCH https://cpoauth.com/api/admin/users/clx... \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{"role":"admin"}'
```

---

#### DELETE /api/admin/users/:id

删除用户。

**响应 (200)**:

```json
{ "success": true }
```

**限制**:
- 不能删除自己的账号
- 不能删除最后一个管理员

**curl 示例**:

```bash
curl -X DELETE https://cpoauth.com/api/admin/users/clx... \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."
```
