# 1.3 用户注册与登录

#### POST /api/auth/login

使用邮箱和密码登录。如果用户开启了 2FA，会返回 2FA 挑战信息。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| email | string | 是 | 用户邮箱 |
| password | string | 是 | 密码 |
| turnstileToken | string | 否 | Turnstile 验证令牌（如果站点启用了 Turnstile） |

**响应 (200 - 登录成功，无 2FA)**:

```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "id": "clx...",
    "username": "Alice",
    "handle": "alice",
    "displayName": null,
    "email": "alice@example.com"
  }
}
```

**响应 (200 - 需要 2FA)**:

```json
{
  "requiresTwoFactor": true,
  "method": "email_otp",
  "challengeId": "550e8400-e29b-41d4-a716-446655440000"
}
```

**错误码**: `400` 缺少参数/验证码失败, `401` 凭据错误, `503` SMTP 未配置

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"alice@example.com","password":"mypassword"}'
```

---

#### POST /api/auth/2fa/verify-login

完成登录后的 2FA 验证。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| challengeId | string | 是 | 上一步获取的 challenge ID |
| code | string | 是 | 验证码（邮箱 OTP 或 TOTP） |

**响应 (200)**:

```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "id": "clx...",
    "username": "Alice",
    "handle": "alice",
    "displayName": null,
    "email": "alice@example.com"
  }
}
```

**错误码**: `400` 缺少参数或挑战过期/无效, `401` 验证码错误或 2FA 不可用

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/2fa/verify-login \
  -H "Content-Type: application/json" \
  -d '{"challengeId":"550e8400-...","code":"123456"}'
```

---

#### POST /api/auth/passkey/login/options

开始 Passkey 登录，获取 WebAuthn 认证选项。需要提供邮箱。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| email | string | 是 | 用户邮箱 |

**响应 (200)**:

```json
{
  "challengeId": "550e8400-e29b-41d4-a716-446655440000",
  "options": {
    "challenge": "...",
    "allowCredentials": [
      { "type": "public-key", "id": "base64...", "transports": ["internal"] }
    ],
    "timeout": 60000,
    "rpId": "cpoauth.com"
  }
}
```

**错误码**: `400` 缺少邮箱, `404` 该邮箱尚未注册 Passkey

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/passkey/login/options \
  -H "Content-Type: application/json" \
  -d '{"email":"alice@example.com"}'
```

---

#### POST /api/auth/passkey/login/verify

验证 Passkey 登录认证结果。如果用户开启了 2FA，会返回 2FA 挑战。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| challengeId | string | 是 | 上一步的 challenge ID |
| response | object | 是 | 浏览器 WebAuthn `navigator.credential.get()` 的响应 |

**响应 (200 - 无 2FA)**:

```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": { "id": "clx...", "username": "Alice", ... }
}
```

**响应 (200 - 需要 2FA)**:

```json
{
  "requiresTwoFactor": true,
  "method": "totp",
  "challengeId": "550e8400-..."
}
```

**错误码**: `400` 缺少参数或挑战过期, `401` 验证失败, `503` SMTP 未配置

---

#### POST /api/auth/register

注册新用户。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| username | string | 是 | 用户名（需符合验证规则） |
| email | string | 是 | 邮箱地址 |
| password | string | 是 | 密码 |
| turnstileToken | string | 否 | Turnstile 验证令牌 |

**响应 (200)**:

```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": { "id": "clx...", "username": "Alice", "handle": "alice", "displayName": null, "email": "alice@example.com" }
}
```

**注意**: 第一个注册的用户自动成为管理员。注册后会自动发送验证邮件（如果配置了 SMTP）。

**错误码**: `400` 缺少字段/用户名无效/验证码失败, `403` 注册已关闭, `409` 用户已存在

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username":"alice","email":"alice@example.com","password":"securepass"}'
```

---

#### GET /api/auth/verify

邮箱验证链接（点击后重定向到前端页面）。

**查询参数**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| token | string | 是 | 邮件中的验证令牌 |

**响应**: 重定向到 `/email-verified?status=success` 或 `/email-verified?status=error`

---

#### POST /api/auth/password/forgot

发送密码重置邮件。为防邮箱枚举，无论邮箱是否存在都返回成功。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| email | string | 是 | 注册邮箱 |

**响应 (200)**:

```json
{ "success": true }
```

**错误码**: `400` 缺少邮箱

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/password/forgot \
  -H "Content-Type: application/json" \
  -d '{"email":"alice@example.com"}'
```

---

#### POST /api/auth/password/reset

使用重置令牌重置密码。同时撤销所有现有的 OAuth token 和 session。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| token | string | 是 | 邮件中的重置令牌 |
| newPassword | string | 是 | 新密码（至少 8 位） |

**响应 (200)**:

```json
{ "success": true }
```

**错误码**: `400` 缺少参数/密码太短/令牌无效或过期

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/password/reset \
  -H "Content-Type: application/json" \
  -d '{"token":"abc123...","newPassword":"newsecurepass"}'
```
