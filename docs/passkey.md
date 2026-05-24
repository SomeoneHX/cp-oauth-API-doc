# 2.3 Passkey

#### POST /api/auth/passkey/register/options

获取 Passkey 注册选项（WebAuthn 创建凭证选项）。

**响应 (200)**: WebAuthn `PublicKeyCredentialCreationOptions` 对象

```json
{
  "rp": { "name": "CP OAuth", "id": "cpoauth.com" },
  "user": { "id": "...", "name": "alice", "displayName": "Alice" },
  "challenge": "...",
  "pubKeyCredParams": [ { "type": "public-key", "alg": -7 } ],
  "timeout": 60000,
  "excludeCredentials": [],
  "authenticatorSelection": { "residentKey": "required", "userVerification": "preferred" }
}
```

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/passkey/register/options \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."
```

---

#### POST /api/auth/passkey/register/verify

验证并保存 Passkey 注册结果。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| response | object | 是 | 浏览器 `navigator.credentials.create()` 的响应 |
| name | string | 否 | Passkey 名称（默认 "My Passkey"） |

**响应 (200)**:

```json
{ "success": true }
```

---

#### GET /api/auth/passkey/credentials

列出用户的 Passkey 凭证。

**响应 (200)**:

```json
[
  { "id": "clx...", "name": "我的通行密钥", "createdAt": "2025-01-15T10:00:00.000Z" }
]
```

---

#### DELETE /api/auth/passkey/credentials/:id

删除指定的 Passkey 凭证。

**响应 (200)**:

```json
{ "success": true }
```

**错误码**: `404` Passkey 不存在

**curl 示例**:

```bash
curl -X DELETE https://cpoauth.com/api/auth/passkey/credentials/clx... \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."
```
