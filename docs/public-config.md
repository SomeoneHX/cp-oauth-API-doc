# 1.1 站点配置与公共信息

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
