# Aurelia Pay — architecture and security contract

## Product boundary
Aurelia Pay is the planned independent payment orchestration and collection application. It owns provider integrations. Aurelia PMS becomes a client and must not host provider-specific gateway code. Aurelia Pay is provider-independent at its public API boundary, but it is not bank-independent and cannot bypass providers, schemes, banks, merchant requirements or regulation.

## Intended topology
```
PMS / Web / Android / iOS / POS / External Apps
                 |
          SDK / REST / Hosted Checkout
                 |
             Aurelia Pay API
                 |
       Identity + Tenant Isolation
                 |
 Payment Intent / Idempotency / State Machine
                 |
          Provider Adapter Registry
      /       |       |       |       \
 Razorpay  Cashfree  PayU  PhonePe  Paytm ...
                 |
      Verified Webhook + Status Pull
                 |
       Evidence Inbox / Reconciliation
                 |
        Authorized financial event
                 |
       Merchant-owned accounting system
```

## Non-negotiable trust rules
1. Provider secrets, salts, signing keys and webhook secrets remain in server-side secret management. Never ship them in PMS HTML/JavaScript, WebView, APK, browser storage or mobile preferences.
2. Tenant/merchant identity derives from authenticated server-side membership, not caller-supplied identifiers.
3. Each client application has registered identity, scoped credentials and permitted callback URLs.
4. Mutations are authenticated, authorized, schema-validated, rate-limited and audited.
5. Idempotency keys bind merchant, operation, amount/currency and immutable request identity. Replays return original result; mismatched reuse is rejected.
6. Verify provider webhooks using exact documented raw-body/signature scheme and correct environment secret; deduplicate event IDs/payload hashes.
7. Webhooks and client callbacks are signals. Independently fetch provider status before accepting capture where supported.
8. Unknown timeouts remain UNKNOWN / RECONCILIATION_REQUIRED. Never create a second charge because a response was lost.
9. Use integer minor units for all money calculations; amount and currency must match the original intent.
10. Refunds are separate immutable operations; cumulative refunds cannot exceed captured value. Reverse accounting only after provider-confirmed completion.
11. Capture and merchant/PMS ledger posting are separate events. Use an outbox/saga when separate databases prevent one ACID transaction.
12. SMS, notifications, email, UTR and imported statements are evidence, not authoritative settlement by themselves.
13. Device observers require cryptographic proof of possession, replay protection, revocation and merchant/property binding. Device IDs alone are not authentication.
14. Live collection defaults OFF and requires separate evidence-backed release authorization.

## Payment lifecycle
```
CREATED → ORDER_CREATED → AWAITING_PROVIDER
  → CAPTURED_UNPOSTED → POSTED_TO_MERCHANT_LEDGER

timeout / uncertain provider response → UNKNOWN / RECONCILIATION_REQUIRED
verified terminal decline → FAILED
identity / amount / currency mismatch → CONFLICT

refund: REQUESTED → RESERVED → SUBMITTED
  → PROVIDER_CONFIRMED → RECONCILED → ACCOUNTING_REVERSAL_POSTED
```
Client UI cannot set CAPTURED, SETTLED or REFUNDED.

## Integration modes
- Hosted checkout URL.
- Server-to-server REST API.
- JavaScript SDK without provider secrets.
- Native Android/iOS SDK only when required by provider.
- Verified HTTPS app links / universal links for return navigation, never as payment proof.
- Provider-backed expiring payment links and amount/reference-bound QR.
- Signed, replay-protected server webhooks.

No platform can force an arbitrary third-party app to accept an integration; use supported operating-system and provider interfaces.

## Regulatory boundary
Start as payment orchestration using appropriately onboarded licensed providers. Do not hold, pool, route or settle customer funds as an independent payment aggregator without specialist legal/compliance review and any required authorisation. Assess merchant onboarding/KYC, business category, settlement model, escrow and data obligations for the actual business model and jurisdiction.

## Readiness
This is a design contract, not a deployed platform. No production provider, merchant, webhook endpoint, payment ledger or live collection capability is asserted by this initial research transfer.
