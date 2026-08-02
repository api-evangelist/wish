---
name: Fulfill Wish orders
description: List released Wish orders, ship them with tracking, and handle refunds correctly using the Wish Marketplace V3 API.
api: openapi/wish-marketplace-v3-openapi.json
operations: [GetMultipleOrders, GetOrder, GetShippingCarriers, ShipOrder, GetValidRefundReasons, RefundOrder]
---

# Fulfill Wish orders

Authenticate every call with `Authorization: Bearer <token>` (OAuth 2.0, scopes `orders:read` + `orders:write`). Base URL `https://merchant.wish.com/api/v3` (sandbox: `https://sandbox.merchant.wish.com/api/v3`).

1. **List orders to fulfill** — `GetMultipleOrders` (`GET /api/v3/orders`). Paginate keyset-style with `limit` + `created_at_min`/`created_at_max` and `sort_by=created_at.asc`; page forward by passing the last order's timestamp. Use the `fields` parameter to request only what you need.
2. **Inspect a single order** — `GetOrder` (`GET /api/v3/orders/{id}`).
3. **Resolve the carrier** — `GetShippingCarriers` (`GET /api/v3/orders/shipping_carriers`) and match your carrier's accepted identifier.
4. **Ship / update tracking** — `ShipOrder` (`PUT /api/v3/orders/{id}/tracking`) with the tracking number and carrier. There is **no idempotency key** on this API: on a timeout, re-read the order with `GetOrder` before retrying the write.
5. **Refund or cancel when needed** — first `GetValidRefundReasons` (`GET /api/v3/orders/{id}/refund_reasons`), then `RefundOrder` (`PUT /api/v3/orders/{id}/refund`) with a valid reason code.

Every response uses the `{message, code, data}` envelope — `code: 0` means success; non-zero codes carry the error in `message`. Respect `Wish-Rate-Limit-Remaining` and back off on `429` until `Wish-Rate-Limit-Reset`. Log `Wish-Request-Id` from each response for support escalation. Prefer webhook topics `ORDER_RELEASE` and `ORDER_FULFILLMENT_DEADLINE` over polling (see skills/wish-webhook-subscriptions.md).
