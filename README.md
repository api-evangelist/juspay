# Juspay (juspay)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
