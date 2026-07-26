---
name: Create and fulfil a delivery order
description: Initialize an order at a point of sale, attach the customer, address and menu items, reserve a delivery time, then create and track the delivery to completion using the Deliverart API.
api: openapi/deliverart-openapi.yml
generated: '2026-07-18'
method: generated
source: https://apidoc.deliverart.it/
operations:
  - ordersInit
  - ordersUpdateCustomer
  - ordersUpdateCustomerAddress
  - ordersUpdateItems
  - ordersUpdatePayment
  - reservationReserveDeliveryTime
  - reservationDeliveryTimeDetailByOrder
  - ordersDetailDelivery
  - deliveriesCreate
  - deliveriesTakeCharge
  - deliveriesStart
  - deliveriesOrderDelivered
  - deliveriesEnd
---

# Create and fulfil a delivery order

Operating instructions for an agent driving the Deliverart order-to-delivery flow. Base URL `https://pubapi.deliverart.it`. RPC style: `GET` reads, `POST` writes, all `application/json`.

## Auth
- Send OAuth2 bearer (`Authorization: Bearer <token>` from `POST https://auth.deliverart.it/oauth`, password grant) **or** an API key header `X-Deliverart-ApiKey`.
- Order operations require the `order_admin` / `order_create` / `order_update` scopes; delivery operations require `delivery_admin`. See `scopes/deliverart-scopes.yml`.

## Steps
1. **Init the order** — `ordersInit` (`POST /pos/{id}/order/init`) with the point-of-sale id. Keep the returned order id.
2. **Attach the customer** — `ordersUpdateCustomer` (`POST /order/{id}/update/customer`); optionally `ordersUpdateCustomerAddress` for the delivery address.
3. **Add items** — `ordersUpdateItems` (`POST /order/{id}/update/menu/items`) with the menu item selection.
4. **Set payment** — `ordersUpdatePayment` (`POST /order/{id}/update/payment`).
5. **Reserve a delivery time** — `reservationReserveDeliveryTime` (`POST /workshift/{id}/delivery/time/reserve`); confirm with `reservationDeliveryTimeDetailByOrder`.
6. **Create the delivery** — `deliveriesCreate` (`POST /delivery/create`) for the order.
7. **Track fulfilment** — courier lifecycle: `deliveriesTakeCharge` (`/delivery/{id}/take`) → `deliveriesStart` (`/delivery/{id}/start`) → `deliveriesOrderDelivered` (`/delivery/order/{id}/delivered`) → `deliveriesEnd` (`/delivery/{id}/end`). Read state any time with `ordersDetailDelivery` (`GET /order/{id}/delivery/detail`).

## Rules
- No idempotency-key mechanism is documented (`conventions/deliverart-conventions.yml`) — do **not** blindly retry a `POST` that may have succeeded; re-read order/delivery detail first.
- Dates are UTC; pass `X-Date-TimeZone` to render in a local zone, `X-I18n-Locale` (`it_IT`/`en_GB`/`ar_AE`) for localized text.
- Errors return a JSON envelope (`message`/`code`); handle `422` (validation), `409` (conflict), `401` (auth/scope). See `errors/deliverart-problem-types.yml`.
- To cancel before fulfilment use `ordersAbort`; to remove use `ordersDelete`.
