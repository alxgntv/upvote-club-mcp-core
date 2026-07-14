# Upvote.club Public API — MCP Reference

## Setup (единственный шаг для пользователя)

1. Зарегистрируйся на **https://upvote.club**
2. Оформи план **MATE** (без него API недоступен)
3. Открой **https://upvote.club/api** → сгенерируй API key (`upv_...`)
4. Вставь ключ в настройки MCP-коннектора как `UPVOTE_API_KEY`

Ключ показывается один раз — сохрани его. Перегенерировать можно только снова на https://upvote.club/api

MCP **не** выдаёт и **не** генерирует ключи — только использует уже полученный.

## Auth

| Что | Значение |
|-----|----------|
| Header | `X-API-Key: upv_...` |
| Env var | `UPVOTE_API_KEY` |
| Получить ключ | https://upvote.club/api |

## Pricing

| Rule | Value |
|------|-------|
| Global minimum per action | 2 points |
| GitHub / Product Hunt | 16 points per action |
| Meaningful COMMENT | 64 points per action |
| Charge | `price × actions_required` (plan discount on create) |
| Daily slot | 1 slot per `create_task` |
| Refund | `delete_task` refunds incomplete actions |

## create_task — examples

### Reddit upvotes

```json
{
  "post_url": "https://www.reddit.com/r/startups/comments/abc123/my_launch/",
  "type": "UPVOTE",
  "social_network_code": "REDDIT",
  "price": 4,
  "actions_required": 20
}
```

### Product Hunt (min 16/action)

```json
{
  "post_url": "https://www.producthunt.com/posts/my-product",
  "type": "UPVOTE",
  "social_network_code": "PRODUCTHUNT",
  "price": 16,
  "actions_required": 50
}
```

### Meaningful comment (min 64/action)

```json
{
  "post_url": "https://www.reddit.com/r/SaaS/comments/xyz/post/",
  "type": "COMMENT",
  "social_network_code": "REDDIT",
  "price": 64,
  "actions_required": 5,
  "meaningful_comment": true,
  "meaningful_comments": [{ "text": "Really helpful breakdown." }]
}
```

### MULTI_ACTION bundle

```json
{
  "post_url": "https://twitter.com/user/status/123456789",
  "type": "MULTI_ACTION",
  "social_network_code": "TWITTER",
  "multi_action_items": [
    { "action_type": "LIKE", "price": 2, "actions_required": 10 },
    {
      "action_type": "COMMENT",
      "price": 64,
      "actions_required": 2,
      "meaningful_comment": true,
      "meaningful_comments": [{ "text": "Solid thread." }]
    }
  ]
}
```

## create_task — errors

| HTTP | Error |
|------|-------|
| 401 | Invalid or missing API key |
| 403 | MATE plan required |
| 403 | Profile verification required |
| 400 | Missing required fields |
| 400 | Minimum price per action is 2 points |
| 400 | No available tasks left |
| 400 | Insufficient balance |
| 400 | Duplicate URL+type+platform |
| 400 | meaningful_comments required / no links |

## Tools (6)

| Tool | Auth | Purpose |
|------|------|---------|
| `get_api_reference` | none | Full docs (JSON) |
| `list_platforms` | none | Platforms + min prices |
| `create_task` | API key | Create promotion |
| `get_task_status` | API key | Track progress |
| `delete_task` | API key | Cancel + refund |

Machine-readable: call `get_api_reference` or read `api-reference.js`.
