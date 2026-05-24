# 2.6 账号绑定（竞赛平台）

#### GET /api/account/bindings

列出当前用户绑定的所有竞赛平台账号。

**响应 (200)**:

```json
[
  {
    "id": "clx...",
    "platform": "codeforces",
    "platformUid": "12345",
    "platformUsername": "alice_cf",
    "verifiedAt": "2025-01-15T10:00:00.000Z"
  }
]
```

**curl 示例**:

```bash
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  https://cpoauth.com/api/account/bindings
```

---

#### POST /api/account/bind/request

请求绑定一个竞赛平台账号。返回需要在平台个人资料中设置的验证码。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| platform | string | 是 | 平台名（如 `luogu`, `atcoder`） |
| platformUid | string | 是 | 在该平台的用户 ID |

**响应 (200)**:

```json
{
  "code": "CPOAUTH-ABCD1234",
  "expiresIn": 600
}
```

**错误码**: `400` 缺少参数/不支持的平台, `409` 已绑定该平台/该平台账号已被其他用户绑定

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/account/bind/request \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{"platform":"luogu","platformUid":"12345"}'
```

---

#### POST /api/account/bind/verify

完成账号绑定验证。将验证码设置在平台资料中后，用平台返回的凭证调用此接口。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| platform | string | 是 | 平台名 |
| credential | string | 否（AtCoder 可省略） | 验证凭证 |

**响应 (200)**:

```json
{
  "id": "clx...",
  "platform": "luogu",
  "platformUid": "12345",
  "platformUsername": "alice_lg"
}
```

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/account/bind/verify \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{"platform":"luogu","credential":"CPOAUTH-ABCD1234"}'
```

---

#### DELETE /api/account/bind/:platform

解绑指定平台的账号。

**路径参数**: `platform` — 平台名

**响应 (200)**:

```json
{ "success": true }
```

**curl 示例**:

```bash
curl -X DELETE https://cpoauth.com/api/account/bind/luogu \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."
```

---

#### POST /api/account/refresh-username

刷新已绑定的平台用户名（如 GitHub 或 Codeforces 的用户名变更后）。带有冷却时间。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| platform | string | 是 | 平台名 |
| platformUid | string | 是 | 平台用户 UID |

**响应 (200)**:

```json
{
  "platform": "github",
  "platformUid": "12345",
  "platformUsername": "new_username"
}
```

**错误码**: `400` 不支持刷新该平台/缺少参数, `403` 无权限, `404` 未找到绑定, `429` 冷却中, `502` 获取失败

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/account/refresh-username \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{"platform":"github","platformUid":"12345"}'
```

---

#### POST /api/account/luogu/login-credential

为已绑定 Luogu 账号的用户签发 Luogu 登录凭证（用于 paste-login 快速登录）。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| duration | string | 是 | 有效期（如 `1h`, `24h`, `7d`, `30d`） |

**响应 (200)**: Luogu 登录凭证对象

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/account/luogu/login-credential \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{"duration":"24h"}'
```
