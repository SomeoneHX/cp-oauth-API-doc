# 2.2 双因素认证 (2FA)

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
