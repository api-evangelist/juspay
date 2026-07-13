# Juspay (juspay)

Juspay is an Indian payments orchestration and checkout platform that routes transactions across payment gateways, aggregators, UPI, cards, wallets, and netbanking for merchants, banks, and card networks. Its server-to-server Express Checkout / PG REST APIs create and track orders, process transactions, issue refunds, and manage customers, and pair with the HyperSDK / Hyper Checkout drop-in checkout.

## Access Model (Read This First)

Juspay is **not** a public self-serve API. Access is **enterprise / merchant-onboarding based**:

- You onboard as a merchant and are provisioned with an **API key** and a **merchant ID**.
- Authentication is **HTTP Basic** — the API key is the **username** and the **password is an empty string** — sent as `Authorization: Basic <base64(apiKey:)>`. Every request also carries an **`x-merchantid`** header, and Juspay recommends an **`x-routing-id`** header (typically the `customer_id`) to pin the payment session lifecycle.
- There are two hosts: **Sandbox** `https://sandbox.juspay.in` (integrate/test) and **Production** `https://api.juspay.in` (live).
- There is **no public pricing page** (`juspay.io/in/pricing` returns 404 as of this review); pricing is a negotiated per-transaction / orchestration commercial arrangement. In India, bank-to-bank UPI is zero-MDR by NPCI mandate.
- Client-side SDK calls use a short-lived (15-minute) **client auth token** minted by the create-order or get/create-customer endpoints, rather than the raw API key.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/juspay/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/juspay/refs/heads/main/apis.yml)

## Tags

- Payments
- Payment Orchestration
- Checkout
- India
- UPI
- Cards
- Payment Gateway
- Fintech
- HyperSDK
- Financial Infrastructure

## Timestamps

- **Created:** 2026-07-12
- **Modified:** 2026-07-12

## APIs

### Juspay Orders API

Create an order (`POST /orders`), retrieve its current status by order id (`GET /orders/{order_id}`), and read the encrypted status variant (`POST /v4/order-status`). The order is the unit a payment is attached to; a transaction is successful only when the order status reads `CHARGED`.

- **Human URL:** [https://juspay.io/in/docs/api-reference/docs/express-checkout/create-order-api](https://juspay.io/in/docs/api-reference/docs/express-checkout/create-order-api)
- **Base URL:** `https://api.juspay.in`

#### Tags

- Orders
- Payments
- Order Status

#### Properties

- [Documentation](https://juspay.io/in/docs/api-reference/docs/express-checkout/create-order-api)
- [API Reference](https://juspay.io/in/docs/api-reference/docs/express-checkout/order-status-api)
- [OpenAPI](openapi/juspay-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)

### Juspay Transactions API

Server-to-server transaction endpoint (`POST /txns`) that processes a payment against an existing order with a chosen payment method — cards, UPI, netbanking, or wallets. Documented as exempt from the standard order authentication flow in the Express Checkout reference.

- **Human URL:** [https://juspay.io/in/docs/api-reference/docs/express-checkout/introduction](https://juspay.io/in/docs/api-reference/docs/express-checkout/introduction)
- **Base URL:** `https://api.juspay.in`

#### Tags

- Transactions
- Payments
- Server to Server

#### Properties

- [Documentation](https://juspay.io/in/docs/api-reference/docs/express-checkout/introduction)
- [OpenAPI](openapi/juspay-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)

### Juspay Session API

Create a payment session (`POST /session`) that accepts order parameters and returns an SDK payload and payment links to initialize a Hyper Checkout / HyperSDK drop-in payment experience on web or mobile.

- **Human URL:** [https://juspay.io/in/docs/hyper-checkout/web/base-sdk-integration/session](https://juspay.io/in/docs/hyper-checkout/web/base-sdk-integration/session)
- **Base URL:** `https://api.juspay.in`

#### Tags

- Session
- Checkout
- HyperSDK

#### Properties

- [Documentation](https://juspay.io/in/docs/hyper-checkout/web/base-sdk-integration/session)
- [OpenAPI](openapi/juspay-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)

### Juspay Refunds API

Create a refund for a `CHARGED` order (`POST /orders/{order_id}/refunds`) with a unique request id and amount. Supports `STANDARD`, `INSTANT`, and `INSTANT_WITH_FALLBACK` refund types depending on payment-source availability.

- **Human URL:** [https://juspay.io/in/docs/api-reference/docs/express-checkout/refund-order-api](https://juspay.io/in/docs/api-reference/docs/express-checkout/refund-order-api)
- **Base URL:** `https://api.juspay.in`

#### Tags

- Refunds
- Payments
- Reconciliation

#### Properties

- [Documentation](https://juspay.io/in/docs/api-reference/docs/express-checkout/refund-order-api)
- [OpenAPI](openapi/juspay-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)

### Juspay Customers API

Create a customer (`POST /customers`) and retrieve one by id (`GET /customers/{customer_id}`). Get/create customer can also mint a 15-minute client auth token used for client-side SDK authentication.

- **Human URL:** [https://juspay.io/in/docs/api-reference/docs/express-checkout/createcustomer](https://juspay.io/in/docs/api-reference/docs/express-checkout/createcustomer)
- **Base URL:** `https://api.juspay.in`

#### Tags

- Customers
- Tokenization
- Client Auth Token

#### Properties

- [Documentation](https://juspay.io/in/docs/api-reference/docs/express-checkout/createcustomer)
- [API Reference](https://juspay.io/in/docs/api-reference/docs/express-checkout/getcustomer)
- [OpenAPI](openapi/juspay-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)

## Common Properties

- [Domain Security](security/juspay-domain-security.yml)
- [Authentication](authentication/juspay-authentication.yml)
- [GitHub Organization](https://github.com/juspay)
- [LinkedIn](https://www.linkedin.com/company/juspay-technologies)
- [Website](https://juspay.io/in)
- [Documentation](https://juspay.io/in/docs/api-reference/docs/express-checkout/introduction)
- [Plans](plans/juspay-plans-pricing.yml)
- [Rate Limits](rate-limits/juspay-rate-limits.yml)
- [Fin Ops](finops/juspay-finops.yml)

## Review

A WebSocket review was completed on 2026-07-12: **Juspay does not expose a documented public WebSocket API.** Its public surface is request/response REST (Express Checkout / PG + Hyper Checkout session) with asynchronous payment/refund outcomes delivered via HTTP webhooks. See [review.yml](review.yml).

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
