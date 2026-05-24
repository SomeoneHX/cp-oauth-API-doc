# OAuth Access Token

用于第三方应用通过 OAuth 2.0 流程获取用户资源（UserInfo 端点）。

- 通过 `POST /api/oauth/token` 获取
- 用于 `GET /api/oauth/userinfo` — 需要 `Authorization: Bearer <token>`
- JWT payload 包含 `type: 'oauth_access'`，由 `/api/oauth/userinfo` 处理函数自行验证
