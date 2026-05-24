# 1.6 第三方登录启动（条件公开）

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
