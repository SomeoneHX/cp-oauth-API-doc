# 1.2 用户公开资料

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
