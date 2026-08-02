---
name: Manage Wish products and variations
description: Create and update products, variations, inventory and pricing on the Wish marketplace using the V3 API.
api: openapi/wish-marketplace-v3-openapi.json
operations: [getCategories, getCategoryAttributes, createProduct, createVariation, updateProduct, getProduct, listProductsSync, listProductUpdateRequests, getProductUpdateRequest]
---

# Manage Wish products and variations

Authenticate with `Authorization: Bearer <token>` (scopes `products:read` + `products:write`). Base URL `https://merchant.wish.com/api/v3`.

1. **Pick the category** — `getCategories` (`GET /api/v3/products/categories`) and `getCategoryAttributes` (`GET /api/v3/products/attributes`) to learn required/optional attributes for the category.
2. **Create the product** — `createProduct` (`POST /api/v3/products`) with the category-appropriate attributes. Product writes are **asynchronous**: the response points at a create/update request.
3. **Track the async request** — `listProductUpdateRequests` (`GET /api/v3/products/requests`) and `getProductUpdateRequest` (`GET /api/v3/products/requests/{id}`) until the request completes.
4. **Add variations** — `createVariation` (`POST /api/v3/products/{id}/variations`) for size/color SKUs.
5. **Update price/inventory** — `updateProduct` (`PUT /api/v3/products/{id}`); confirm with `getProduct` (`GET /api/v3/products/{id}`).
6. **Audit the catalog** — `listProductsSync` (`GET /api/v3/products`) with keyset pagination (`limit`, `id_min`/`id_max` — inclusive, so +/- 1 the boundary ID) and partial responses via `fields`.

For catalogs beyond a few hundred items use the bulk jobs: `getProducts` (`POST /api/v3/products/bulk_get`) then `getGetProductsStatus`, and `updateProducts` (`POST /api/v3/products/bulk_update`) then `getUpdateProductsStatus`. All responses use the `{message, code, data}` envelope (`code: 0` = success); there is no idempotency key, so verify state with a read before retrying any write.
