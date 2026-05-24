## 通用 HTTP 状态码

| 状态码 | 含义 | 说明 |
|--------|------|------|
| 200 | OK | 请求成功 |
| 400 | Bad Request | 参数缺失、格式错误、业务校验失败 |
| 401 | Unauthorized | 认证失败（token 无效/过期、密码错误等） |
| 403 | Forbidden | 权限不足（非管理员访问管理员端点） |
| 404 | Not Found | 资源不存在 |
| 405 | Method Not Allowed | 请求方法不支持 |
| 409 | Conflict | 资源冲突（用户名/邮箱/平台账号已存在等） |
| 429 | Too Many Requests | 请求频率限制（如用户名刷新冷却） |
| 500 | Internal Server Error | 服务器内部错误 |
| 502 | Bad Gateway | 上游 API 请求失败 |
| 503 | Service Unavailable | 服务不可用（SMTP 未配置、第三方登录未配置等） |

---
