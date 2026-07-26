---
name: Build and maintain a menu
description: Create a menu with categories, ingredients, allergens and items, translate them for multilingual points of sale, and suspend or resume items in real time using the Deliverart API.
api: openapi/deliverart-openapi.yml
generated: '2026-07-18'
method: generated
source: https://apidoc.deliverart.it/
operations:
  - menuCreate
  - menuCategoriesCreate
  - menuIngredientsCreate
  - menuAllergensCreate
  - menuItemsCreate
  - menuItemsUpdate
  - menuItemsTranslate
  - menuItemsSuspend
  - menuItemsResume
  - menuCategoriesSort
  - menuDetail
---

# Build and maintain a menu

Operating instructions for an agent managing a Deliverart menu. Base URL `https://pubapi.deliverart.it`; `GET` reads, `POST` writes.

## Auth
- OAuth2 bearer or `X-Deliverart-ApiKey`. Menu writes require `menu_admin` (plus `menu_item_admin` / `menu_item_category_admin` / `menu_item_ingredient_admin` / `menu_item_allergen_admin`); reads require `menu_read`. See `scopes/deliverart-scopes.yml`.

## Steps
1. **Create the menu** — `menuCreate` (`POST /menu/create`). Read back with `menuDetail` (`GET /menu/{id}/detail`).
2. **Add categories** — `menuCategoriesCreate` (`POST /menu/category/create`); order them with `menuCategoriesSort` (`POST /menu/category/{id}/sort`).
3. **Define building blocks** — `menuIngredientsCreate` (`POST /menu/ingredient/create`) and `menuAllergensCreate` (`POST /menu/allergen/create`).
4. **Add items** — `menuItemsCreate` (`POST /menu/item/create`), referencing categories, ingredients and allergens; edit with `menuItemsUpdate`.
5. **Localize** — `menuItemsTranslate` (`POST /menu/item/{id}/translate`) for each locale (`it_IT`, `en_GB`, `ar_AE`).
6. **Real-time availability** — `menuItemsSuspend` (`POST /menu/item/{id}/suspend`) to hide a sold-out item, `menuItemsResume` to bring it back.

## Rules
- Menu items support a "rules" composition logic (basic ingredients / variants) — inspect item detail before editing.
- No idempotency keys; verify with a `GET` detail call before retrying a failed create. See `conventions/deliverart-conventions.yml`.
- Localize text with `X-I18n-Locale`; all timestamps are UTC. Errors follow the shared JSON envelope (`errors/deliverart-problem-types.yml`).
