# 可用 Scope 列表

| Scope | 说明 | 对应 UserInfo 字段 |
|-------|------|-------------------|
| `openid` | 基础身份标识 | `sub` |
| `profile` | 用户名、头像、简介 | `username`, `display_name`, `avatar_url`, `bio` |
| `email` | 邮箱地址和验证状态 | `email`, `email_verified` |
| `cp:linked` | 已关联的竞赛平台账号 | `linked_accounts` |
| `link:luogu` | 读取 Luogu 关联信息 | `linked_accounts`（过滤） |
| `link:atcoder` | 读取 AtCoder 关联信息 | `linked_accounts`（过滤） |
| `link:leetcode` | 读取 LeetCode 关联信息 | `linked_accounts`（过滤） |
| `link:codeforces` | 读取 Codeforces 关联信息 | `linked_accounts`（过滤） |
| `link:github` | 读取 GitHub 关联信息 | `linked_accounts`（过滤） |
| `link:google` | 读取 Google 关联信息 | `linked_accounts`（过滤） |
| `link:clist` | 读取 Clist.by 关联信息 | `linked_accounts`（过滤） |
| `cp:summary` | CP Rating 汇总统计 | `cp_summary` |
| `cp:details` | CP 详细赛绩和 Rating 变化 | `cp_details` |
