# 1.4 OAuth 2.0 授权

#### GET /.well-known/openid-configuration

OIDC Discovery 文档（符合 OpenID Connect Discovery 规范）。

**响应 (200)**:

```json
{
  "issuer": "https://cpoauth.com",
  "authorization_endpoint": "https://cpoauth.com/oauth/authorize",
  "token_endpoint": "https://cpoauth.com/api/oauth/token",
  "userinfo_endpoint": "https://cpoauth.com/api/oauth/userinfo",
  "revocation_endpoint": "https://cpoauth.com/api/oauth/revoke",
  "scopes_supported": [
    "openid", "profile", "email", "cp:linked",
    "link:luogu", "link:atcoder", "link:leetcode",
    "link:codeforces", "link:github", "link:google", "link:clist",
    "cp:summary", "cp:details"
  ],
  "response_types_supported": ["code"],
  "response_modes_supported": ["query"],
  "grant_types_supported": ["authorization_code", "refresh_token"],
  "token_endpoint_auth_methods_supported": ["client_secret_post", "none"],
  "revocation_endpoint_auth_methods_supported": ["client_secret_post"],
  "code_challenge_methods_supported": ["S256", "plain"],
  "subject_types_supported": ["public"],
  "claim_types_supported": ["normal"],
  "claims_supported": [
    "sub", "username", "display_name", "avatar_url", "bio",
    "email", "email_verified", "linked_accounts",
    "link_scopes", "cp_summary", "cp_details"
  ],
  "claims_parameter_supported": false,
  "request_parameter_supported": false,
  "request_uri_parameter_supported": false
}
```

**缓存**: 3600 秒

**curl 示例**:

```bash
curl https://cpoauth.com/.well-known/openid-configuration
```

---

#### GET /api/oauth/authorize

OAuth 授权页面的数据接口。返回客户端信息和请求的 Scope。

**查询参数**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| client_id | string | 是 | OAuth 客户端 ID |
| redirect_uri | string | 是 | 必须匹配注册时填写的 URI |
| response_type | string | 是 | 必须为 `code` |
| scope | string | 是 | 以空格分隔的 Scope 列表 |
| state | string | 否 | CSRF 防护的随机值（建议提供） |
| code_challenge | string | 否 | PKCE challenge |
| code_challenge_method | string | 否 | `S256` 或 `plain` |

**响应 (200)**:

```json
{
  "client": { "name": "My App", "clientId": "abc123..." },
  "scopes": ["openid", "profile", "email"],
  "redirectUri": "https://client.example.com/callback",
  "state": "xyz789...",
  "codeChallenge": null,
  "codeChallengeMethod": null
}
```

**错误码**: `400` 不支持的 response_type/缺少参数/无效 redirect_uri/无效 scope, `404` 未知 client_id

**curl 示例**:

```bash
curl "https://cpoauth.com/api/oauth/authorize?client_id=abc123&redirect_uri=https://client.example.com/callback&response_type=code&scope=openid%20profile"
```

---

#### POST /api/oauth/token

OAuth Token 端点。支持 `authorization_code` 和 `refresh_token` 两种 grant type。

**请求体** (`grant_type=authorization_code`):

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| grant_type | string | 是 | `authorization_code` |
| code | string | 是 | 授权码 |
| redirect_uri | string | 是 | 必须与授权请求一致 |
| client_id | string | 是 | 客户端 ID |
| client_secret | string | 否* | 客户端密钥（如果未使用 PKCE 则必填） |
| code_verifier | string | 否* | PKCE verifier（如果使用了 PKCE 则必填） |

\* PKCE 与 client_secret 二选一

**请求体** (`grant_type=refresh_token`):

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| grant_type | string | 是 | `refresh_token` |
| refresh_token | string | 是 | 刷新令牌 |
| client_id | string | 是 | 客户端 ID |
| client_secret | string | 是 | 客户端密钥 |

**响应 (200)**:

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIs...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "a1b2c3d4e5f6...",
  "scope": "openid profile email"
}
```

**说明**:
- access_token 有效期 1 小时
- refresh_token 有效期 30 天
- 使用 Refresh Token Rotation：每次刷新时旧 refresh_token 被撤销，返回新的配对

**错误码**: `400` 缺少参数/无效 code/已使用/已过期/PKCE 校验失败/参数不匹配/不支持的 grant_type, `401` 客户端认证失败

**curl 示例 (authorization_code)**:

```bash
curl -X POST https://cpoauth.com/api/oauth/token \
  -H "Content-Type: application/json" \
  -d '{
    "grant_type": "authorization_code",
    "code": "abc123...",
    "redirect_uri": "https://client.example.com/callback",
    "client_id": "abc123...",
    "code_verifier": "verifier_string..."
  }'
```

**curl 示例 (refresh_token)**:

```bash
curl -X POST https://cpoauth.com/api/oauth/token \
  -H "Content-Type: application/json" \
  -d '{
    "grant_type": "refresh_token",
    "refresh_token": "a1b2c3d4e5f6...",
    "client_id": "abc123...",
    "client_secret": "secret..."
  }'
```

---

#### POST /api/oauth/revoke

OAuth Token 撤销（RFC 7009）。支持撤销 access_token 和 refresh_token。撤销 refresh_token 时会同时删除所有关联的 access_token。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| token | string | 是 | 要撤销的 token |
| token_type_hint | string | 否 | `access_token` 或 `refresh_token` |
| client_id | string | 是 | 客户端 ID（用于认证） |
| client_secret | string | 是 | 客户端密钥 |

**响应 (200 - 根据 RFC 7009 始终返回成功)**:

```json
{ "success": true }
```

**错误码**: `400` 缺少 token, `401` 客户端认证失败

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/oauth/revoke \
  -H "Content-Type: application/json" \
  -d '{
    "token": "eyJhbGciOiJIUzI1NiIs...",
    "token_type_hint": "access_token",
    "client_id": "abc123...",
    "client_secret": "secret..."
  }'
```

---

#### GET /api/oauth/userinfo

OAuth UserInfo 端点（符合 OpenID Connect Core 1.0 规范）。使用 OAuth Access Token 认证（非 Session Token）。

**请求头**: `Authorization: Bearer <access_token>`

**响应 (200 - 根据 scope 动态返回)**:

仅 `openid`:
```json
{ "sub": "clx..." }
```

含 `profile`:
```json
{
  "sub": "clx...",
  "username": "alice",
  "display_name": "Alice",
  "avatar_url": null,
  "bio": "CP enthusiast"
}
```

含 `email`:
```json
{
  "email": "alice@example.com",
  "email_verified": true
}
```

含 `cp:linked`:
```json
{
  "linked_accounts": [
    { "platform": "codeforces", "platformUid": "12345", "platformUsername": "alice_cf" }
  ]
}
```

含 `cp:summary`:
```json
{
  "cp_summary": {
    "available": true,
    "accounts": [
      {
        "resource": "codeforces.com",
        "resource_name": "Codeforces",
        "handle": "alice_cf",
        "rating": 1800,
        "n_contests": 42,
        "resource_rank": null,
        "last_activity": "2025-01-10T00:00:00Z"
      }
    ],
    "highest_rating": { "resource": "codeforces.com", "resource_name": "Codeforces", "handle": "alice_cf", "rating": 1850 },
    "total_contests": 42
  }
}
```

含 `cp:details`:
```json
{
  "cp_details": {
    "available": true,
    "rating_history": [
      {
        "resource": "codeforces.com",
        "resource_name": "Codeforces",
        "contest_id": 1234,
        "event": "Round #1000",
        "date": "2025-01-10T00:00:00Z",
        "handle": "alice_cf",
        "place": 50,
        "old_rating": 1750,
        "new_rating": 1800,
        "rating_change": 50
      }
    ]
  }
}
```

**注意**: `cp:summary` 和 `cp:details` 需要用户绑定 Clist 账号，未绑定时返回 `available: false` 及提示信息。

**错误码**: `401` 缺少/无效 token / token 已撤销或过期, `404` 用户不存在

**curl 示例**:

```bash
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  https://cpoauth.com/api/oauth/userinfo
```
