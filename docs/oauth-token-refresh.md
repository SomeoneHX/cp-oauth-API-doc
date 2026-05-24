# Token 刷新（Refresh Token Rotation）

```
第三方应用                           CP OAuth
    |                                  |
    |  POST /api/oauth/token           |
    |  grant_type=refresh_token        |
    |  refresh_token=<old>             |
    |  client_id=<id>                  |
    |  client_secret=<secret>          |
    |--------------------------------->|
    |                                  |  1. 验证旧的 refresh_token
    |                                  |  2. 标记旧的为 revoked
    |                                  |  3. 签发新的配对 (access + refresh)
    |  { access_token,                 |
    |    refresh_token (new),          |
    |    expires_in: 3600 }            |
    |<---------------------------------|
```
