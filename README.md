# CP OAuth API 文档

> CP OAuth 是一个面向竞赛编程（Competitive Programming）社区的 OAuth 2.0 授权服务，支持用户注册、竞赛平台账号关联以及第三方应用授权，完整实现 Authorization Code + PKCE 流程。

## 目录

- [CP OAuth API 文档](#cp-oauth-api-文档)
  - [目录](#目录)
  - [基本信息](#基本信息)
  - [认证方式](#认证方式)
    - [Auth Session Token](#auth-session-token)
    - [OAuth Access Token](#oauth-access-token)
    - [管理员权限](#管理员权限)
  - [通用 HTTP 状态码](#通用-http-状态码)
  - [一、公开 API](#一公开-api)
    - [1.1 站点配置与公共信息](#11-站点配置与公共信息)
      - [GET /api/public/config](#get-apipublicconfig)
      - [GET /api/public/hitokoto](#get-apipublichitokoto)
      - [GET /api/public/stats](#get-apipublicstats)
      - [GET /api/public/notices](#get-apipublicnotices)
      - [GET /api/public/showcase](#get-apipublicshowcase)
    - [1.2 用户公开资料](#12-用户公开资料)
      - [GET /api/users](#get-apiusers)
      - [GET /api/users/:username](#get-apiusersusername)
      - [GET /api/users/:username/card.svg](#get-apiusersusernamecardsvg)
    - [1.3 用户注册与登录](#13-用户注册与登录)
      - [POST /api/auth/login](#post-apiauthlogin)
      - [POST /api/auth/2fa/verify-login](#post-apiauth2faverify-login)
      - [POST /api/auth/passkey/login/options](#post-apiauthpasskeyloginoptions)
      - [POST /api/auth/passkey/login/verify](#post-apiauthpasskeyloginverify)
      - [POST /api/auth/register](#post-apiauthregister)
      - [GET /api/auth/verify](#get-apiauthverify)
      - [POST /api/auth/password/forgot](#post-apiauthpasswordforgot)
      - [POST /api/auth/password/reset](#post-apiauthpasswordreset)
    - [1.4 OAuth 2.0 授权](#14-oauth-20-授权)
      - [GET /.well-known/openid-configuration](#get-well-knownopenid-configuration)
      - [GET /api/oauth/authorize](#get-apioauthauthorize)
      - [POST /api/oauth/token](#post-apioauthtoken)
      - [POST /api/oauth/revoke](#post-apioauthrevoke)
      - [GET /api/oauth/userinfo](#get-apioauthuserinfo)
    - [1.5 第三方登录（回调）](#15-第三方登录回调)
      - [POST /api/auth/thirdparty/github/callback](#post-apiauththirdpartygithubcallback)
      - [POST /api/auth/thirdparty/google/callback](#post-apiauththirdpartygooglecallback)
      - [POST /api/auth/thirdparty/codeforces/callback](#post-apiauththirdpartycodeforcescallback)
      - [POST /api/auth/thirdparty/clist/callback](#post-apiauththirdpartyclistcallback)
      - [POST /api/auth/thirdparty/leetcode/register/request](#post-apiauththirdpartyleetcoderegisterrequest)
      - [POST /api/auth/thirdparty/leetcode/register/verify](#post-apiauththirdpartyleetcoderegisterverify)
      - [POST /api/auth/thirdparty/luogu/paste-login](#post-apiauththirdpartyluogupaste-login)
      - [POST /api/auth/thirdparty/luogu/register/request](#post-apiauththirdpartyluoguregisterrequest)
      - [POST /api/auth/thirdparty/luogu/register/verify](#post-apiauththirdpartyluoguregisterverify)
      - [POST /api/auth/thirdparty/luogu/challenge/request](#post-apiauththirdpartyluoguchallengerequest)
      - [POST /api/auth/thirdparty/luogu/challenge/verify](#post-apiauththirdpartyluoguchallengeverify)
    - [1.6 第三方登录启动（条件公开）](#16-第三方登录启动条件公开)
      - [GET /api/auth/thirdparty/github/start](#get-apiauththirdpartygithubstart)
      - [GET /api/auth/thirdparty/google/start](#get-apiauththirdpartygooglestart)
      - [GET /api/auth/thirdparty/codeforces/start](#get-apiauththirdpartycodeforcesstart)
      - [GET /api/auth/thirdparty/clist/start](#get-apiauththirdpartycliststart)
  - [二、需认证 API](#二需认证-api)
    - [2.1 用户信息与管理](#21-用户信息与管理)
      - [GET /api/auth/me](#get-apiauthme)
      - [PATCH /api/auth/me](#patch-apiauthme)
      - [POST /api/auth/verify](#post-apiauthverify)
      - [POST /api/auth/password/change](#post-apiauthpasswordchange)
    - [2.2 双因素认证 (2FA)](#22-双因素认证-2fa)
      - [GET /api/auth/2fa/status](#get-apiauth2fastatus)
      - [POST /api/auth/2fa/disable](#post-apiauth2fadisable)
      - [POST /api/auth/2fa/setup/totp/request](#post-apiauth2fasetuptotprequest)
      - [POST /api/auth/2fa/setup/totp/confirm](#post-apiauth2fasetuptotpconfirm)
      - [POST /api/auth/2fa/setup/email/request](#post-apiauth2fasetupemailrequest)
      - [POST /api/auth/2fa/setup/email/confirm](#post-apiauth2fasetupemailconfirm)
    - [2.3 Passkey](#23-passkey)
      - [POST /api/auth/passkey/register/options](#post-apiauthpasskeyregisteroptions)
      - [POST /api/auth/passkey/register/verify](#post-apiauthpasskeyregisterverify)
      - [GET /api/auth/passkey/credentials](#get-apiauthpasskeycredentials)
      - [DELETE /api/auth/passkey/credentials/:id](#delete-apiauthpasskeycredentialsid)
    - [2.4 OAuth 客户端管理](#24-oauth-客户端管理)
      - [GET /api/oauth/clients](#get-apioauthclients)
      - [POST /api/oauth/clients](#post-apioauthclients)
      - [DELETE /api/oauth/clients/:id](#delete-apioauthclientsid)
    - [2.5 OAuth 授权管理](#25-oauth-授权管理)
      - [POST /api/oauth/authorize](#post-apioauthauthorize)
      - [GET /api/oauth/authorized-apps](#get-apioauthauthorized-apps)
      - [DELETE /api/oauth/authorized-apps/:clientId](#delete-apioauthauthorized-appsclientid)
    - [2.6 账号绑定（竞赛平台）](#26-账号绑定竞赛平台)
      - [GET /api/account/bindings](#get-apiaccountbindings)
      - [POST /api/account/bind/request](#post-apiaccountbindrequest)
      - [POST /api/account/bind/verify](#post-apiaccountbindverify)
      - [DELETE /api/account/bind/:platform](#delete-apiaccountbindplatform)
      - [POST /api/account/refresh-username](#post-apiaccountrefresh-username)
      - [POST /api/account/luogu/login-credential](#post-apiaccountluogulogin-credential)
  - [三、管理员 API](#三管理员-api)
    - [3.1 用户管理](#31-用户管理)
      - [GET /api/admin/users](#get-apiadminusers)
      - [PATCH /api/admin/users/:id](#patch-apiadminusersid)
      - [DELETE /api/admin/users/:id](#delete-apiadminusersid)
    - [3.2 系统配置](#32-系统配置)
      - [GET /api/admin/config](#get-apiadminconfig)
      - [PATCH /api/admin/config](#patch-apiadminconfig)
    - [3.3 公告管理](#33-公告管理)
      - [GET /api/admin/notices](#get-apiadminnotices)
      - [POST /api/admin/notices](#post-apiadminnotices)
      - [DELETE /api/admin/notices/:id](#delete-apiadminnoticesid)
    - [3.4 展示墙管理](#34-展示墙管理)
      - [GET /api/admin/showcase](#get-apiadminshowcase)
      - [POST /api/admin/showcase](#post-apiadminshowcase)
      - [DELETE /api/admin/showcase/:id](#delete-apiadminshowcaseid)
  - [四、OAuth 2.0 授权流程详解](#四oauth-20-授权流程详解)
    - [可用 Scope 列表](#可用-scope-列表)
    - [Authorization Code + PKCE 流程](#authorization-code--pkce-流程)
    - [Token 刷新（Refresh Token Rotation）](#token-刷新refresh-token-rotation)
    - [Token 撤销（RFC 7009）](#token-撤销rfc-7009)
  - [五、错误码参考](#五错误码参考)

---

## 基本信息

- **Base URL**: 由 `PUBLIC_BASE_URL` 环境变量配置
- **API 前缀**: `/api/`
- **请求格式**: `application/json`（部分 OAuth 端点也接受 `application/x-www-form-urlencoded`）
- **响应格式**: `application/json`（`/api/users/:username/card.svg` 返回 `image/svg+xml`）

---

## 认证方式

### Auth Session Token

用于管理用户自身的资源（个人信息、OAuth 客户端、授权应用、账号绑定等）。

**认证方式**:
- `Authorization: Bearer <token>` HTTP 请求头
- 登录或注册成功后返回

**中间件行为** (`server/middleware/auth-session.ts`):
- 如果请求携带 `Bearer` token，验证 JWT 并在 Redis 中检查 session 有效性。无效或过期返回 **401**。
- 如果不携带 `Bearer` token，中间件直接放行，由处理函数自行决定是否需要认证。
- 特例：`/api/oauth/userinfo` 排除在中间件之外，使用 OAuth Access Token 认证。

### OAuth Access Token

用于第三方应用通过 OAuth 2.0 流程获取用户资源（UserInfo 端点）。

- 通过 `POST /api/oauth/token` 获取
- 用于 `GET /api/oauth/userinfo` — 需要 `Authorization: Bearer <token>`
- JWT payload 包含 `type: 'oauth_access'`，由 `/api/oauth/userinfo` 处理函数自行验证

### 管理员权限

管理员端点（`/api/admin/*`）调用 `requireAdmin()`，同时验证:
1. 请求携带有效的 Auth Session Token
2. 用户角色为 `admin`

不满足条件时:
- 无 token 或无有效 token 返回 **401**
- 非 admin 角色返回 **403**

---

## 通用 HTTP 状态码

| 状态码 | 含义 | 说明 |
|--------|------|------|
| 200 | OK | 请求成功 |
| 400 | Bad Request | 参数缺失、格式错误、业务校验失败 |
| 401 | Unauthorized | 认证失败（token 无效/过期、密码错误等） |
| 403 | Forbidden | 权限不足（非管理员访问管理员端点） |
| 404 | Not Found | 资源不存在 |
| 405 | Method Not Allowed | 请求方法不支持 |
| 409 | Conflict | 资源冲突（用户名/邮箱/平台账号已存在等） |
| 429 | Too Many Requests | 请求频率限制（如用户名刷新冷却） |
| 500 | Internal Server Error | 服务器内部错误 |
| 502 | Bad Gateway | 上游 API 请求失败 |
| 503 | Service Unavailable | 服务不可用（SMTP 未配置、第三方登录未配置等） |

---

## 一、公开 API

### 1.1 站点配置与公共信息

#### GET /api/public/config

返回公开的站点配置（注册开关、Turnstile 公钥、OAuth 提供商可用性等）。

**响应 (200)**:

```json
{
  "siteTitle": "CP OAuth",
  "registrationEnabled": true,
  "recentUsersCount": 6,
  "turnstileEnabled": true,
  "turnstileSiteKey": "0x4AAAA...",
  "codeforcesLoginEnabled": true,
  "githubLoginEnabled": true,
  "googleLoginEnabled": true,
  "clistLoginEnabled": false
}
```

**缓存**: Redis 60 秒

**curl 示例**:

```bash
curl https://cpoauth.com/api/public/config
```

---

#### GET /api/public/hitokoto

返回一条随机一言（从 hitokoto.cn 获取，失败时返回默认文案）。

**响应 (200)**:

```json
{
  "text": "Competitive programming is not only about solving problems...",
  "source": "CP OAuth",
  "fromWho": null
}
```

**curl 示例**:

```bash
curl https://cpoauth.com/api/public/hitokoto
```

---

#### GET /api/public/stats

返回站点公开统计数据。

**响应 (200)**:

```json
{
  "users": 1234,
  "linkedAccounts": 5678,
  "oauthClients": 42,
  "oauthLoginRequestsToday": 89
}
```

| 字段 | 说明 |
|------|------|
| users | 注册用户总数 |
| linkedAccounts | 关联竞赛平台账号总数 |
| oauthClients | OAuth 应用总数 |
| oauthLoginRequestsToday | 今日 OAuth 授权请求次数 |

**缓存**: 60 秒

**curl 示例**:

```bash
curl https://cpoauth.com/api/public/stats
```

---

#### GET /api/public/notices

返回站点公告列表（最多 3 条，置顶优先、按发布时间倒序）。内容已做 HTML 安全过滤。

**响应 (200)**:

```json
[
  {
    "id": 1,
    "title": "系统维护通知",
    "content": "<p>本周六凌晨 2:00-4:00 进行系统维护</p>",
    "pinned": true,
    "publishedAt": "2025-01-15T10:00:00.000Z"
  }
]
```

**curl 示例**:

```bash
curl https://cpoauth.com/api/public/notices
```

---

#### GET /api/public/showcase

返回展示墙项目（分 site 和 project 两类）。

**响应 (200)**:

```json
{
  "sites": [
    {
      "id": 1,
      "category": "site",
      "name": "My OAuth App",
      "description": "一个使用 CP OAuth 的示例应用",
      "url": "https://example.com",
      "iconUrl": null
    }
  ],
  "projects": [
    {
      "id": 2,
      "category": "project",
      "name": "CP Stats Bot",
      "description": "Discord 机器人",
      "url": "https://github.com/example/bot",
      "iconUrl": null
    }
  ]
}
```

**curl 示例**:

```bash
curl https://cpoauth.com/api/public/showcase
```

---

### 1.2 用户公开资料

#### GET /api/users

返回最近注册的用户列表。

**查询参数**:

| 参数 | 类型 | 必填 | 默认 | 说明 |
|------|------|------|------|------|
| limit | number | 否 | 50 | 返回数量（最大 50） |

**响应 (200)**:

```json
[
  {
    "id": "clx...",
    "username": "alice",
    "displayName": "Alice",
    "bio": "CP enthusiast",
    "avatarUrl": null,
    "createdAt": "2025-01-15T10:00:00.000Z"
  }
]
```

**缓存**: Redis 120 秒

**curl 示例**:

```bash
curl https://cpoauth.com/api/users?limit=10
```

---

#### GET /api/users/:username

返回用户公开资料，包括绑定的竞赛平台账号（根据隐私设置过滤），以及可选的 CP Rating 统计和 Rating 历史（从 Clist.by 获取）。

**路径参数**:

| 参数 | 说明 |
|------|------|
| username | 用户名（不区分大小写） |

**响应 (200)**:

```json
{
  "id": "clx...",
  "username": "alice",
  "displayName": "Alice",
  "bio": "CP enthusiast",
  "homepage": "https://example.com",
  "avatarUrl": null,
  "createdAt": "2025-01-15T10:00:00.000Z",
  "linkedAccounts": [
    {
      "platform": "codeforces",
      "platformUid": "12345",
      "platformUsername": "alice_cf"
    }
  ],
  "cpStats": {
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
    "highest_rating": {
      "resource": "codeforces.com",
      "resource_name": "Codeforces",
      "handle": "alice_cf",
      "rating": 1850
    },
    "total_contests": 42
  },
  "ratingHistory": [
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
```

**注意**: `cpStats` 和 `ratingHistory` 仅在用户开启了对应隐私设置（`publicCpStats` / `publicRatingHistory`）且绑定了 Clist 账号时返回。

**错误码**: `404` 用户不存在, `409` 用户名存在歧义

**curl 示例**:

```bash
curl https://cpoauth.com/api/users/alice
```

---

#### GET /api/users/:username/card.svg

动态生成 SVG Profile Card，用于展示用户的平台账号信息。

**路径参数**:

| 参数 | 说明 |
|------|------|
| username | 用户名 |

**查询参数**:

| 参数 | 类型 | 必填 | 默认 | 说明 |
|------|------|------|------|------|
| width | number | 否 | 480 | 卡片宽度（300-800） |
| theme | string | 否 | light | 主题（`light` 或 `dark`） |
| lang | string | 否 | en | 语言（`en` / `zh` / `ja`） |

**响应**: `image/svg+xml`

**缓存**: 3600 秒

**curl 示例**:

```bash
curl -o card.svg "https://cpoauth.com/api/users/alice/card.svg?width=480&theme=light&lang=zh"
```

---

### 1.3 用户注册与登录

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

---

### 1.4 OAuth 2.0 授权

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

---

### 1.5 第三方登录（回调）

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

---

### 1.6 第三方登录启动（条件公开）

这四个端点根据 `mode` 参数决定是否要求认证：

- `mode=login` 或 `mode=register`：**公开**（Register 模式需要 Turnstile）
- `mode=bind`：**需要 Auth Session Token**

#### GET /api/auth/thirdparty/github/start

获取 GitHub 授权 URL。

**查询参数**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| mode | string | 否 | `login`（默认）、`register`、`bind` |
| redirect | string | 否 | 登录后重定向路径（必须以 `/` 开头） |
| turnstileToken | string | 否* | Register 模式需要 |

**响应 (200)**:

```json
{
  "authorizationUrl": "https://github.com/login/oauth/authorize?client_id=..."
}
```

**错误码**: `503` GitHub 登录未配置

**curl 示例**:

```bash
curl "https://cpoauth.com/api/auth/thirdparty/github/start?mode=login&redirect=/dashboard"
```

---

#### GET /api/auth/thirdparty/google/start

获取 Google 授权 URL。参数结构同上。

**curl 示例**:

```bash
curl "https://cpoauth.com/api/auth/thirdparty/google/start?mode=login"
```

---

#### GET /api/auth/thirdparty/codeforces/start

获取 Codeforces 授权 URL。参数结构同上。

**curl 示例**:

```bash
curl "https://cpoauth.com/api/auth/thirdparty/codeforces/start?mode=login"
```

---

#### GET /api/auth/thirdparty/clist/start

获取 Clist.by 授权 URL（使用 PKCE）。参数结构同上，内部会自动生成 code_verifier 和 code_challenge。

**curl 示例**:

```bash
curl "https://cpoauth.com/api/auth/thirdparty/clist/start?mode=login"
```

---

## 二、需认证 API

以下所有端点需要在请求头中携带 Auth Session Token：

```
Authorization: Bearer <token>
```

Token 通过 `POST /api/auth/login` 或 `POST /api/auth/register` 获取。

### 2.1 用户信息与管理

#### GET /api/auth/me

获取当前登录用户的详细信息。

**响应 (200)**:

```json
{
  "id": "clx...",
  "email": "alice@example.com",
  "username": "alice",
  "displayName": "Alice",
  "bio": "CP enthusiast",
  "homepage": null,
  "avatarUrl": null,
  "role": "user",
  "emailVerified": true,
  "publicLinkedPlatforms": ["codeforces", "github"],
  "publicLinkedPlatformsConfigured": true,
  "publicCpStats": true,
  "publicRatingHistory": false,
  "theme": "system",
  "locale": "zh"
}
```

**curl 示例**:

```bash
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  https://cpoauth.com/api/auth/me
```

---

#### PATCH /api/auth/me

更新当前用户的资料和隐私设置。

**请求体**（所有字段可选）:

| 参数 | 类型 | 说明 |
|------|------|------|
| username | string | 新用户名（需唯一且符合规则） |
| displayName | string | 显示名称 |
| bio | string | 个人简介 |
| homepage | string | 个人主页 URL |
| avatarUrl | string | 头像 URL |
| email | string | 新邮箱（会发送验证邮件，重置验证状态） |
| theme | string | 主题偏好（`light` / `dark` / `system`） |
| locale | string | 语言偏好（`en` / `zh` / `ja`） |
| publicLinkedPlatforms | string[] | 公开显示的竞赛平台列表 |
| publicCpStats | boolean | 是否公开 CP Rating 统计 |
| publicRatingHistory | boolean | 是否公开 Rating 变化历史 |

**响应 (200)**: 同 `GET /api/auth/me`

**错误码**: `400` 用户名无效/邮箱格式错误/publicLinkedPlatforms 需为数组, `409` 用户名/邮箱已被占用, `503` SMTP 未配置（改邮箱时）

**curl 示例**:

```bash
curl -X PATCH https://cpoauth.com/api/auth/me \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{"displayName":"Alice", "publicCpStats":true, "locale":"zh"}'
```

---

#### POST /api/auth/verify

重新发送邮箱验证邮件。

**请求体**: 无

**响应 (200)**:

```json
{ "success": true, "alreadyVerified": true }
```

**错误码**: `400` OAuth 生成的占位邮箱无法验证, `404` 用户不存在, `503` SMTP 未配置

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/verify \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."
```

---

#### POST /api/auth/password/change

修改密码。同时撤销所有现有的 OAuth token 和 session。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| currentPassword | string | 是 | 当前密码 |
| newPassword | string | 是 | 新密码（至少 8 位） |

**响应 (200)**:

```json
{ "success": true }
```

**错误码**: `400` 缺少参数/密码太短, `401` 当前密码错误, `404` 用户不存在

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/password/change \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{"currentPassword":"oldpass","newPassword":"newpass123"}'
```

---

### 2.2 双因素认证 (2FA)

#### GET /api/auth/2fa/status

获取当前用户的 2FA 状态。

**响应 (200)**:

```json
{
  "twoFactorEnabled": true,
  "twoFactorMethod": "totp",
  "hasEmail": true,
  "passkeyCount": 2
}
```

**curl 示例**:

```bash
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  https://cpoauth.com/api/auth/2fa/status
```

---

#### POST /api/auth/2fa/disable

关闭 2FA。需要验证当前密码。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| password | string | 是 | 当前密码 |

**响应 (200)**:

```json
{ "success": true }
```

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/2fa/disable \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{"password":"currentpass"}'
```

---

#### POST /api/auth/2fa/setup/totp/request

开始设置 TOTP 2FA。生成密钥、OTP Auth URL 和 QR Code Data URL。

**响应 (200)**:

```json
{
  "secret": "JBSWY3DPEHPK3PXP",
  "otpauth": "otpauth://totp/CP%20OAuth:alice@example.com?secret=...",
  "qrCodeDataUrl": "data:image/png;base64,..."
}
```

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/2fa/setup/totp/request \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."
```

---

#### POST /api/auth/2fa/setup/totp/confirm

确认 TOTP 设置。验证一次性验证码后启用 2FA。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| code | string | 是 | TOTP 应用生成的 6 位验证码 |

**响应 (200)**:

```json
{ "success": true }
```

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/2fa/setup/totp/confirm \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{"code":"123456"}'
```

---

#### POST /api/auth/2fa/setup/email/request

开始设置邮箱 OTP 2FA。向用户注册邮箱发送 6 位验证码。

**响应 (200)**:

```json
{ "success": true }
```

**错误码**: `503` SMTP 未配置

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/auth/2fa/setup/email/request \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."
```

---

#### POST /api/auth/2fa/setup/email/confirm

确认邮箱 OTP 设置。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| code | string | 是 | 邮箱中收到的 6 位验证码 |

**响应 (200)**:

```json
{ "success": true }
```

---

### 2.3 Passkey

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

---

### 2.4 OAuth 客户端管理

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

---

### 2.5 OAuth 授权管理

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

---

### 2.6 账号绑定（竞赛平台）

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

---

## 三、管理员 API

管理员 API 除了需要 Bearer token 外，还要求用户角色为 `admin`。

### 3.1 用户管理

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

---

### 3.2 系统配置

#### GET /api/admin/config

获取所有系统配置项。

**响应 (200)**:

```json
{
  "site_title": "CP OAuth",
  "registration_enabled": "true",
  "home_recent_users_count": "6",
  "smtp_host": "smtp.example.com",
  "smtp_port": "587",
  "smtp_user": "user@example.com",
  "smtp_pass": "***",
  "smtp_from": "noreply@example.com",
  "turnstile_enabled": "true",
  "turnstile_site_key": "0x4AAAA...",
  "turnstile_secret_key": "0x...",
  "codeforces_client_id": "...",
  "codeforces_client_secret": "...",
  "clist_client_id": "...",
  "clist_client_secret": "...",
  "github_client_id": "...",
  "github_client_secret": "...",
  "google_client_id": "...",
  "google_client_secret": "...",
  "username_refresh_cooldown": "1440"
}
```

**curl 示例**:

```bash
curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  https://cpoauth.com/api/admin/config
```

---

#### PATCH /api/admin/config

更新系统配置项。仅允许更新预定义的 key 列表。

**请求体**: 键值对对象，key 必须是允许的配置项

```json
{
  "site_title": "CP OAuth",
  "registration_enabled": "true",
  "turnstile_site_key": "0x4AAAA..."
}
```

**响应 (200)**: 返回所有配置项（同 GET）

**允许的配置项**: `site_title`, `registration_enabled`, `home_recent_users_count`, `smtp_host`, `smtp_port`, `smtp_user`, `smtp_pass`, `smtp_from`, `turnstile_enabled`, `turnstile_site_key`, `turnstile_secret_key`, `codeforces_client_id`, `codeforces_client_secret`, `clist_client_id`, `clist_client_secret`, `github_client_id`, `github_client_secret`, `google_client_id`, `google_client_secret`, `username_refresh_cooldown`

**curl 示例**:

```bash
curl -X PATCH https://cpoauth.com/api/admin/config \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{"site_title":"My OAuth Server"}'
```

---

### 3.3 公告管理

#### GET /api/admin/notices

列出所有公告（最多 50 条，置顶优先）。

**响应 (200)**:

```json
{
  "notices": [
    {
      "id": 1,
      "title": "系统维护通知",
      "content": "<p>本周六凌晨 2:00-4:00 进行系统维护</p>",
      "pinned": true,
      "publishedAt": "2025-01-15T10:00:00.000Z",
      "createdAt": "2025-01-15T10:00:00.000Z",
      "updatedAt": "2025-01-15T10:00:00.000Z"
    }
  ]
}
```

---

#### POST /api/admin/notices

创建公告。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| title | string | 是 | 标题（最多 120 字） |
| content | string | 是 | 内容（HTML） |
| pinned | boolean | 否 | 是否置顶 |

**响应 (200)**:

```json
{ "notice": { "id": 2, "title": "...", ... } }
```

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/admin/notices \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{"title":"系统维护","content":"<p>通知内容</p>","pinned":true}'
```

---

#### DELETE /api/admin/notices/:id

删除公告。

**响应 (200)**:

```json
{ "success": true }
```

---

### 3.4 展示墙管理

#### GET /api/admin/showcase

列出所有展示墙项目。

**响应 (200)**:

```json
{
  "items": [
    {
      "id": 1,
      "category": "site",
      "name": "My App",
      "description": "描述",
      "url": "https://example.com",
      "iconUrl": null,
      "sortOrder": 0,
      "createdAt": "...",
      "updatedAt": "..."
    }
  ]
}
```

---

#### POST /api/admin/showcase

创建展示墙项目。

**请求体**:

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| category | string | 是 | `site` 或 `project` |
| name | string | 是 | 名称 |
| description | string | 否 | 描述 |
| url | string | 是 | 链接 |
| iconUrl | string | 否 | 图标 URL |
| sortOrder | number | 否 | 排序权重 |

**curl 示例**:

```bash
curl -X POST https://cpoauth.com/api/admin/showcase \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..." \
  -H "Content-Type: application/json" \
  -d '{"category":"site","name":"My App","url":"https://example.com"}'
```

---

#### DELETE /api/admin/showcase/:id

删除展示墙项目。

**响应 (200)**:

```json
{ "success": true }
```

---

## 四、OAuth 2.0 授权流程详解

### 可用 Scope 列表

| Scope | 说明 | 对应 UserInfo 字段 |
|-------|------|-------------------|
| `openid` | 基础身份标识 | `sub` |
| `profile` | 用户名、头像、简介 | `username`, `display_name`, `avatar_url`, `bio` |
| `email` | 邮箱地址和验证状态 | `email`, `email_verified` |
| `cp:linked` | 已关联的竞赛平台账号 | `linked_accounts` |
| `link:luogu` | 读取 Luogu 关联信息 | `linked_accounts`（过滤） |
| `link:atcoder` | 读取 AtCoder 关联信息 | `linked_accounts`（过滤） |
| `link:leetcode` | 读取 LeetCode 关联信息 | `linked_accounts`（过滤） |
| `link:codeforces` | 读取 Codeforces 关联信息 | `linked_accounts`（过滤） |
| `link:github` | 读取 GitHub 关联信息 | `linked_accounts`（过滤） |
| `link:google` | 读取 Google 关联信息 | `linked_accounts`（过滤） |
| `link:clist` | 读取 Clist.by 关联信息 | `linked_accounts`（过滤） |
| `cp:summary` | CP Rating 汇总统计 | `cp_summary` |
| `cp:details` | CP 详细赛绩和 Rating 变化 | `cp_details` |

### Authorization Code + PKCE 流程

```
第三方应用                           CP OAuth                         用户浏览器
    |                                  |                                  |
    |  1. 发起授权请求                   |                                  |
    |  (client_id, scope,              |                                  |
    |   redirect_uri, code_challenge)   |                                  |
    |--------------------------------->|                                  |
    |                                  |  2. 显示授权页面                  |
    |                                  |--------------------------------->|
    |                                  |                                  |
    |                                  |  3. 用户同意/拒绝                |
    |                                  |<---------------------------------|
    |                                  |                                  |
    |  4. 授权码回调                    |                                  |
    |  (code + state)                  |                                  |
    |<---------------------------------|                                  |
    |                                  |                                  |
    |  5. 换取 Token                   |                                  |
    |  (code + code_verifier +         |                                  |
    |   grant_type=authorization_code)  |                                  |
    |--------------------------------->|                                  |
    |                                  |                                  |
    |  6. 返回 Token                   |                                  |
    |  (access_token + refresh_token)  |                                  |
    |<---------------------------------|                                  |
    |                                  |                                  |
    |  7. 使用 Token 访问 UserInfo     |                                  |
    |  (Authorization: Bearer)          |                                  |
    |--------------------------------->|                                  |
    |                                  |                                  |
    |  8. 返回用户信息                 |                                  |
    |<---------------------------------|                                  |
```

### Token 刷新（Refresh Token Rotation）

```
第三方应用                           CP OAuth
    |                                  |
    |  POST /api/oauth/token           |
    |  grant_type=refresh_token        |
    |  refresh_token=<old>             |
    |  client_id=<id>                  |
    |  client_secret=<secret>          |
    |--------------------------------->|
    |                                  |  1. 验证旧的 refresh_token
    |                                  |  2. 标记旧的为 revoked
    |                                  |  3. 签发新的配对 (access + refresh)
    |  { access_token,                 |
    |    refresh_token (new),          |
    |    expires_in: 3600 }            |
    |<---------------------------------|
```

### Token 撤销（RFC 7009）

```
第三方应用                           CP OAuth
    |                                  |
    |  POST /api/oauth/revoke          |
    |  token=<token>                   |
    |  token_type_hint=refresh_token   |
    |  client_id=<id>                  |
    |  client_secret=<secret>          |
    |--------------------------------->|
    |                                  |  撤销 token（始终返回 200）
    |  { success: true }               |
    |<---------------------------------|
```

---

## 五、错误码参考

| 状态码 | 常见场景 |
|--------|---------|
| **400** | — 请求参数缺失或格式错误<br>— 用户名/邮箱格式无效<br>— OAuth code 无效/已使用/已过期<br>— PKCE code_verifier 校验失败<br>— 不支持的 OAuth grant_type<br>— 无效的 redirect_uri<br>— 验证码错误/过期<br>— 没有待处理的绑定请求<br>— 不支持的平台 |
| **401** | — Auth Session Token 缺失/无效/过期<br>— OAuth Access Token 缺失/无效/过期<br>— 密码错误<br>— 2FA 验证码错误<br>— OAuth 客户端凭据错误<br>— Linked account 不匹配 |
| **403** | — 非管理员访问管理员端点<br>— 注册功能已关闭<br>— 无权刷新用户名 |
| **404** | — 用户不存在<br>— OAuth 客户端不存在<br>— OAuth 授权码无效<br>— Passkey 不存在<br>— 未找到绑定记录<br>— Luogu 剪贴板不存在 |
| **405** | — 请求方法不支持 |
| **409** | — 用户名已存在<br>— 邮箱已被注册<br>— 平台账号已被其他用户绑定<br>— 已绑定该平台<br>— OAuth state 参数不匹配 |
| **429** | — 用户名刷新冷却中 |
| **500** | — 服务器内部错误<br>— 无法分配邮箱 |
| **502** | — 上游 API 获取失败（GitHub 等） |
| **503** | — SMTP 未配置<br>— 第三方登录未配置<br>— Turnstile 验证密钥未配置 |
