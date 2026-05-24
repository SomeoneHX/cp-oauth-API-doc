# 3.2 系统配置

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
