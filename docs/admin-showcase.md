# 3.4 展示墙管理

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
