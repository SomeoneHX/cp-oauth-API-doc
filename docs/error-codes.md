## 五、错误码参考

| 状态码 | 常见场景 |
|--------|---------|
| **400** | — 请求参数缺失或格式错误<br>— 用户名/邮箱格式无效<br>— OAuth code 无效/已使用/已过期<br>— PKCE code_verifier 校验失败<br>— 不支持的 OAuth grant_type<br>— 无效的 redirect_uri<br>— 验证码错误/过期<br>— 没有待处理的绑定请求<br>— 不支持的平台 |
| **401** | — Auth Session Token 缺失/无效/过期<br>— OAuth Access Token 缺失/无效/过期<br>— 密码错误<br>— 2FA 验证码错误<br>— OAuth 客户端凭据错误<br>— Linked account 不匹配 |
| **403** | — 非管理员访问管理员端点<br>— 注册功能已关闭<br>— 无权刷新用户名 |
| **404** | — 用户不存在<br>— OAuth 客户端不存在<br>— OAuth 授权码无效<br>— Passkey 不存在<br>— 未找到绑定记录<br>— Luogu 剪贴板不存在 |
| **405** | — 请求方法不支持 |
| **409** | — 用户名已存在<br>— 邮箱已被注册<br>— 平台账号已被其他用户绑定<br>— 已绑定该平台<br>— OAuth state 参数不匹配 |
| **429** | — 用户名刷新冷却中 |
| **500** | — 服务器内部错误<br>— 无法分配邮箱 |
| **502** | — 上游 API 获取失败（GitHub 等） |
| **503** | — SMTP 未配置<br>— 第三方登录未配置<br>— Turnstile 验证密钥未配置 |
