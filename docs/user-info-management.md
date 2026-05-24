# 2.1 用户信息与管理

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
