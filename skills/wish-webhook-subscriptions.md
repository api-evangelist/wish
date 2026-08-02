---
name: Subscribe to Wish webhooks
description: Replace polling with real-time Wish webhook subscriptions for order, product, penalty, ticket and ProductBoost events.
api: openapi/wish-marketplace-v3-openapi.json
operations: [getTopics, createSubscription, listSubscriptions, getSubscription, updateSubscription, deleteSubscription]
---

# Subscribe to Wish webhooks

Authenticate with `Authorization: Bearer <token>` (scopes `webhook:read` + `webhook:write`). Base URL `https://merchant.wish.com/api/v3`. Docs: https://merchant.wish.com/documentation/webhooks

1. **Discover topics** — `getTopics` (`GET /api/v3/webhook/topics`). Topics include `ORDER_ALL`, `ORDER_RELEASE`, `ORDER_FULFILLMENT_DEADLINE`, `ORDER_TRACKING_UPDATE`, `ORDER_REFUND`, `POLICY_PENALTY_ISSUE`, `TICKET_AWAITING_MERCHANT`, `PRODUCT_UPDATE_MERCHANT`, `PRODUCT_INVENTORY_CHANGE_MERCHANT`, `PRODUCT_SOLDOUT`, and the ProductBoost campaign family (full catalog: asyncapi/wish-webhooks.yml).
2. **Create a subscription** — `createSubscription` (`POST /api/v3/webhook/subscriptions`) with your HTTPS endpoint URL and the topic.
3. **Verify and manage** — `listSubscriptions` (`GET /api/v3/webhook/subscriptions`), `getSubscription` (`GET /api/v3/webhook/subscriptions/{id}`), rotate endpoints with `updateSubscription` (`PUT /api/v3/webhook/subscriptions/{id}`), remove with `deleteSubscription` (`DELETE /api/v3/webhook/subscriptions/{id}`).

Wish explicitly recommends webhooks over polling when rate limits (`Wish-Rate-Limit-Remaining`, 429 + `Wish-Rate-Limit-Reset`) constrain you. Responses use the `{message, code, data}` envelope; `code: 0` = success.
