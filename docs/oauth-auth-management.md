# 2.5 OAuth 授权管理

#### POST /api/oauth/authorize

用户确认授权后签发 OAuth 授权码。这是授权流程中用户点击"同意"后的提交端点。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| client_id | string | 是 | 客户端 ID |
| redirect_uri | string | 是 | 重定向 URI |
| scopes | string[] | 是 | 用户同意的 scope 列表 |
| state | string | 否 | 授权请求携带的 state |
| code_challenge | string | 否 | PKCE challenge |
| code_challenge_method | string | 否 | PKCE 方法 |
| approved | boolean | 是 | 用户是否同意授权 |

**响应 (200 - 同意)**:

```json
{
  "redirect": "https://client.example.com/callback?code=abc123...&state=xyz789..."
}
```

**响应 (200 - 拒绝)**:

```json
{
  "redirect": "https://client.example.com/callback?error=access_denied&state=xyz789..."
}
```

**错误码**: `400` 缺少参数/无效 scope/无效 redirect_uri, `404` 未知 client, `401` 未认证

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/oauth/authorize \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{
    "client_id": "abc123...",
    "redirect_uri": "https://client.example.com/callback",
    "scopes": ["openid", "profile"],
    "state": "xyz789...",
    "approved": true
  }'
```

---

#### GET /api/oauth/authorized-apps

列出当前用户已授权的所有 OAuth 应用。

**响应 (200)**:

```json
[
  {
    "clientId": "abc123...",
    "name": "My App",
    "scopes": ["openid", "profile", "email"],
    "latestAuthorizedAt": "2025-01-15T10:00:00.000Z",
    "accessTokenCount": 3,
    "refreshTokenCount": 2
  }
]
```

**curl 示例**:

```bash
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  https://cpoauth.com/api/oauth/authorized-apps
```

---

#### DELETE /api/oauth/authorized-apps/:clientId

撤销指定 OAuth 应用的所有 token。删除所有 access token，吊销所有 refresh token。

**路径参数**: `clientId` — OAuth 客户端 ID

**响应 (200)**:

```json
{
  "success": true,
  "deletedAccessTokens": 3,
  "revokedRefreshTokens": 2
}
```

**curl 示例**:

```bash
curl -X DELETE https://cpoauth.com/api/oauth/authorized-apps/abc123... \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."
```
