# Token 撤销（RFC 7009）

```
第三方应用                           CP OAuth
    |                                  |
    |  POST /api/oauth/revoke          |
    |  token=<token>                   |
    |  token_type_hint=refresh_token   |
    |  client_id=<id>                  |
    |  client_secret=<secret>          |
    |--------------------------------->|
    |                                  |  撤销 token（始终返回 200）
    |  { success: true }               |
    |<---------------------------------|
```
