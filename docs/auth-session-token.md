# Auth Session Token

用于管理用户自身的资源（个人信息、OAuth 客户端、授权应用、账号绑定等）。

**认证方式**:
- `Authorization: Bearer <token>` HTTP 请求头
- 登录或注册成功后返回

**中间件行为** (`server/middleware/auth-session.ts`):
- 如果请求携带 `Bearer` token，验证 JWT 并在 Redis 中检查 session 有效性。无效或过期返回 **401**。
- 如果不携带 `Bearer` token，中间件直接放行，由处理函数自行决定是否需要认证。
- 特例：`/api/oauth/userinfo` 排除在中间件之外，使用 OAuth Access Token 认证。
