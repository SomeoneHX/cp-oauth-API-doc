# 1.5 第三方登录（回调）

大部分第三方登录回调使用 `POST` 方法，接收 `code` 和 `state` 参数（Luogu 和 LeetCode 等非标准流程除外）。

#### POST /api/auth/thirdparty/github/callback

GitHub OAuth 回调。支持三种模式：`login`（登录或自动注册）、`register`（强制注册）、`bind`（绑定到现有用户）。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| code | string | 是 | GitHub 返回的授权码 |
| state | string | 是 | 请求时保存的 state 值 |

**响应 (200 - login 模式)**:

```json
{
  "mode": "login",
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "redirect": "/",
  "user": { "id": "clx...", "username": "Alice", ... }
}
```

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/thirdparty/github/callback \
  -H "Content-Type: application/json" \
  -d '{"code":"abc123...","state":"xyz789..."}'
```

---

#### POST /api/auth/thirdparty/google/callback

Google OAuth 回调。结构与 GitHub 回调相同。

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/thirdparty/google/callback \
  -H "Content-Type: application/json" \
  -d '{"code":"abc123...","state":"xyz789..."}'
```

---

#### POST /api/auth/thirdparty/codeforces/callback

Codeforces OIDC 回调。使用 OpenID Connect Discovery。

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/thirdparty/codeforces/callback \
  -H "Content-Type: application/json" \
  -d '{"code":"abc123...","state":"xyz789..."}'
```

---

#### POST /api/auth/thirdparty/clist/callback

Clist.by OAuth 回调。使用 PKCE 增强安全。

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/thirdparty/clist/callback \
  -H "Content-Type: application/json" \
  -d '{"code":"abc123...","state":"xyz789..."}'
```

---

#### POST /api/auth/thirdparty/leetcode/register/request

LeetCode 注册第一步：请求验证码。需要 LeetCode 用户名（slug）和 Turnstile。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| platformUid | string | 是 | LeetCode 用户名（slug） |
| turnstileToken | string | 是 | Turnstile 验证令牌 |

**响应 (200)**:

```json
{
  "requestId": "a1b2c3d4e5f6...",
  "code": "CPOAUTH-REG-ABCD1234",
  "expiresIn": 600
}
```

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/thirdparty/leetcode/register/request \
  -H "Content-Type: application/json" \
  -d '{"platformUid":"leetcode_user","turnstileToken":"0x..."}'
```

---

#### POST /api/auth/thirdparty/leetcode/register/verify

LeetCode 注册第二步：验证凭证，创建用户。自动生成 `@leetcode.local` 合成邮箱和用户名。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| requestId | string | 是 | 上一步的 requestId |
| credential | string | 是 | 验证凭证（需在 LeetCode 个人简介中设置） |

**响应 (200)**:

```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": { "id": "clx...", "username": "leetcode_user", ... },
  "mode": "register"
}
```

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/thirdparty/leetcode/register/verify \
  -H "Content-Type: application/json" \
  -d '{"requestId":"a1b2c3d4...","credential":"CPOAUTH-REG-ABCD1234"}'
```

---

#### POST /api/auth/thirdparty/luogu/paste-login

Luogu Paste 登录。通过公开的 Luogu 剪贴板验证用户身份。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| pasteId | string | 是 | Luogu 剪贴板 ID |
| turnstileToken | string | 是 | Turnstile 验证令牌 |

**响应 (200)**:

```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": { "id": "clx...", "username": "Alice", ... },
  "loginMethod": "luogu-paste"
}
```

**错误码**: `400` 缺少参数/剪贴板不公开, `401` 凭据无效/账号不匹配, `404` 剪贴板不存在

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/thirdparty/luogu/paste-login \
  -H "Content-Type: application/json" \
  -d '{"pasteId":"abc123","turnstileToken":"0x..."}'
```

---

#### POST /api/auth/thirdparty/luogu/register/request

Luogu 注册第一步：请求验证码。需要 Luogu UID 和 Turnstile。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| platformUid | string | 是 | Luogu 用户 UID |
| turnstileToken | string | 是 | Turnstile 验证令牌 |

**响应 (200)**:

```json
{
  "requestId": "a1b2c3d4e5f6...",
  "code": "CPOAUTH-REG-ABCD1234",
  "expiresIn": 600
}
```

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/thirdparty/luogu/register/request \
  -H "Content-Type: application/json" \
  -d '{"platformUid":"12345","turnstileToken":"0x..."}'
```

---

#### POST /api/auth/thirdparty/luogu/register/verify

Luogu 注册第二步：验证凭证，创建用户。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| requestId | string | 是 | 上一步的 requestId |
| credential | string | 是 | 验证凭证（需在 Luogu 个人简介中设置） |

**响应 (200)**:

```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": { "id": "clx...", "username": "Alice", ... },
  "mode": "register"
}
```

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/thirdparty/luogu/register/verify \
  -H "Content-Type: application/json" \
  -d '{"requestId":"a1b2c3d4...","credential":"CPOAUTH-REG-ABCD1234"}'
```

---

#### POST /api/auth/thirdparty/luogu/challenge/request

Luogu Challenge 登录第一步：请求挑战码。需要 Luogu UID。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| platformUid | string | 是 | Luogu 用户 UID |
| turnstileToken | string | 是 | Turnstile 验证令牌 |

**响应 (200)**:

```json
{
  "requestId": "a1b2c3d4e5f6...",
  "code": "CPOAUTH-CHALLENGE-ABCD1234",
  "expiresIn": 600
}
```

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/thirdparty/luogu/challenge/request \
  -H "Content-Type: application/json" \
  -d '{"platformUid":"12345","turnstileToken":"0x..."}'
```

---

#### POST /api/auth/thirdparty/luogu/challenge/verify

Luogu Challenge 登录第二步：验证凭证，完成登录。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| requestId | string | 是 | 上一步的 requestId |
| credential | string | 是 | 验证凭证 |

**响应 (200)**:

```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": { "id": "clx...", "username": "Alice", ... },
  "loginMethod": "luogu-challenge"
}
```
