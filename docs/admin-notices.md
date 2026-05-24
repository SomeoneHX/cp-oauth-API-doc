# 3.3 公告管理

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
