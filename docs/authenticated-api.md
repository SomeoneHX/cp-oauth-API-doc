## 二、需认证 API

以下所有端点需要在请求头中携带 Auth Session Token：

```
Authorization: Bearer <token>
```

Token 通过 `POST /api/auth/login` 或 `POST /api/auth/register` 获取。
