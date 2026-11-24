# Provider catalogue — source-derived status

This catalogue distinguishes source presence, adapter registration, route exposure, checkout handoff, merchant acceptance and production readiness.

## Snapshot comparison

| Provider | production-adapter branch | multi-provider branch | Evidence caveat |
|---|---|---|---|
| Razorpay | Factory-wired; partial seven-operation adapter; test checkout | Factory-wired; seven-operation adapter; test checkout | Merchant UAT, canonical PMS ledger, device and release acceptance open |
| Cashfree Payments | Factory-wired; partial seven-operation adapter; test checkout | Factory-wired; seven-operation adapter; test checkout | Confirm API version, signature/refund contracts and merchant UAT |
| PayU India | Prototype, not factory-wired | Factory-wired; classic hosted checkout, status, reverse-hash callback and refund lifecycle | Pin classic vs v2 contract and merchant sandbox/UAT |
| PhonePe PG | PG V2 prototype, not factory-wired | Factory-wired; OAuth, Standard Checkout V2, status, webhook auth and refund lifecycle | Account/API version and UAT required |
| Paytm | Not implemented | Factory-wired; checksum initiate/status/callback/refund lifecycle | MID, website/industry configuration and staging/UAT required |
| JioPay / JPSL | Unregistered hosted-checkout prototype | Factory-wired; hosted checkout, HMAC-SHA256, status, webhook and refund command | Merchant/aggregator setup and UAT required |
| CCAvenue | Not implemented; merchant kit required | Narrower test checkout/status/encrypted callback adapter | Standard refund and refund-status not supported in cited snapshot; merchant kit/IP allowlisting required |

## Provider details

### Razorpay
Source: `payment-backend/src/providers/razorpay.mjs`.
- API base in source: `https://api.razorpay.com/v1`.
- Credentials: Key ID, Key Secret, Webhook Secret.
- Operations: credential validation, order creation, receipt lookup, payment status, raw-body HMAC webhook verification, refund creation and refund status.
- Test/live key prefix is checked against environment; amount input uses integer minor units.
- Merchant UAT, canonical folio posting, physical-device acceptance and release approval remain separate.

### Cashfree Payments
Source: `payment-backend/src/providers/cashfree.mjs`.
- Sandbox: `https://sandbox.cashfree.com/pg`; live: `https://api.cashfree.com/pg`.
- Credentials: App ID, Secret Key, Webhook Secret.
- Source versions differ: `2023-08-01` in one production-adapter snapshot and `2026-01-01` in the multi-provider snapshot. Confirm current supported version before adopting.
- Webhook verification uses timestamp + raw body HMAC-SHA256 and a timestamp age check.
- Amounts normalize to integer paise; merchant UAT and ledger integration remain open.

### PayU India
Source: `payment-backend/src/providers/payu-adapter.mjs`.
- Classic hosted checkout uses merchant key + salt and payment hash / reverse-hash callback verification.
- Test/live host selection and status/refund command logic are represented.
- Classic and PayU v2 do not share assumed authentication/payload contracts. Pin exact product/version and merchant onboarding contract first.
- Earlier branch prototype is a different API contract; do not combine it blindly.

### PhonePe PG
Source: `payment-backend/src/providers/phonepe-adapter.mjs`.
- Standard Checkout V2 with OAuth client credentials.
- Registry fields: Client ID, Client Secret, Client Version, Webhook Username and Webhook Password.
- Explicit test/UAT and production endpoint selection.
- Account-specific API version and UAT must be confirmed.

### Paytm
Source: `payment-backend/src/providers/paytm-adapter.mjs`.
- Staging/production host selection.
- Credentials include MID, merchant key and website name in the inspected source.
- Requests and responses use Paytm checksum signing/verification.
- Initiate, status, callback and refund lifecycle in later source snapshot; merchant staging/UAT required.

### JioPay / JPSL
Source: `payment-backend/src/providers/jiopay-adapter.mjs`.
- UAT/production host selection.
- Merchant ID and salt/hash key in source registry.
- HMAC-SHA256 over alphabetically sorted scalar field values, excluding `secureHash`; timing-safe comparison.
- Hosted checkout, status, callback and refund command flow represented; merchant/aggregator UAT required.

### CCAvenue
Source: `payment-backend/src/providers/ccavenue.mjs`.
- Test/live hosted-checkout and API endpoint selection.
- Merchant ID, Access Code and Working Key.
- Encrypted hosted request/response and order-status API handling.
- Inspected source uses AES-128-CBC, MD5-derived key and fixed IV. This is recorded as observed legacy source behavior, **not recommended as a new cryptographic design**. A fresh implementation must follow the current merchant integration kit and current security guidance.
- Ordinary refund creation/refund-status polling not implemented in cited snapshot; split-refund support must not be represented as a standard refund lifecycle.

## Acceptance stages
Source `docs/SEVEN_PROVIDER_ACCEPTANCE.md` defines:
`NOT_STARTED → CREDENTIALS_VALIDATED → SANDBOX_IN_PROGRESS → SANDBOX_PASSED → SECURITY_REVIEWED → OPERATIONS_REVIEWED → PROVIDER_ACCEPTED → PRODUCTION_ELIGIBLE → LIVE_AUTHORIZED`.

The source states `LIVE_AUTHORIZED` is not implemented and live collection remains disabled. Synthetic tests are not merchant sandbox evidence. Changes to adapter, merchant account, API version, webhook config or endpoint invalidate affected evidence.

## Official documentation entry points
- Razorpay: https://razorpay.com/docs/
- Cashfree: https://www.cashfree.com/docs/
- PayU: https://docs.payu.in/
- PhonePe PG V2: https://developer.phonepe.com/phonepe-payment-gateway/pg-v2-standard-checkout-integration
- Paytm: https://www.paytmpayments.com/docs/
- JioPay: https://docs.jiopay.in/docs/integration-steps
- CCAvenue: https://www.ccavenue.com/faq

Recheck official documentation for exact merchant product, API version, region, environment and current policy before implementing or enabling a provider.
