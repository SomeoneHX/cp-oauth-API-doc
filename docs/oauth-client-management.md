# 2.4 OAuth 客户端管理

#### GET /api/oauth/clients

列出当前用户创建的所有 OAuth 客户端。

**响应 (200)**:

```json
[
  {
    "id": "clx...",
    "clientId": "abc123...",
    "name": "My App",
    "redirectUris": ["https://client.example.com/callback"],
    "createdAt": "2025-01-15T10:00:00.000Z"
  }
]
```

**curl 示例**:

```bash
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  https://cpoauth.com/api/oauth/clients
```

---

#### POST /api/oauth/clients

创建新的 OAuth 客户端。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| name | string | 是 | 应用名称 |
| redirectUris | string[] | 是 | 允许的重定向 URI 列表 |

**响应 (200)**:

```json
{
  "id": "clx...",
  "clientId": "abc123...",
  "name": "My App",
  "redirectUris": ["https://client.example.com/callback"],
  "createdAt": "2025-01-15T10:00:00.000Z",
  "clientSecret": "base64url_secret..." 
}
```

**重要**: `clientSecret` **仅在创建时返回一次**，请立即保存。

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/oauth/clients \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{"name":"My App", "redirectUris":["https://client.example.com/callback"]}'
```

---

#### DELETE /api/oauth/clients/:id

删除指定的 OAuth 客户端。

**路径参数**: `id` — 内部 ID（非 clientId）

**响应 (200)**:

```json
{ "success": true }
```

**错误码**: `404` 客户端不存在或不属当前用户
