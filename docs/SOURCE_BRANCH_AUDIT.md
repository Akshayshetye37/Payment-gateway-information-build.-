# Aurelia-apk payment-provider source and branch audit

Audit date: 2026-11-24.

## Repository metadata
- Source: `Akshayshetye37/Aurelia-apk`, public, default branch `main`.
- Destination: `Akshayshetye37/Payment-gateway-information-build.-`, public, default branch `main`.
- GitHub branch enumeration returned 74 source branch names.

## Payment-specific branches found
- `feature/multi-provider-payment-backend`
- `feature/payment-provider-production-adapters`
- `feature/cloud-provider-abstraction`
- `feature/direct-upi-reconciliation`
- `feature/pay-bridge-platform`
- `fix/payment-dispatch-boundary`
- `hardening/payment-appliance-durability-current`
- `work/payment-ack-proof-hardening`
- `backup/pre-pay-bridge-20261030`
- `backup/pre-upi-safeguards-2026-10-30`
- `backup/pre-settlement-command-gate-2026-11-08`

Payment-adjacent branches include `integration/ledger-patch`, `integration/ledger-patch-pr42-rebase`, `integration/ledger-on-security`, `backup/pre-release-html-update-2026-11-07`, `backup/pre-local-first-cutover-2026-11-07`, and PMS persistence/release work.

## Branch findings

### main
README describes an initial single-HTML PMS upload/scaffold and says the source is incomplete. It does not document the later multi-provider backend.

### feature/payment-provider-production-adapters
At this snapshot:
- Razorpay and Cashfree have factory-wired partial adapters and restricted test checkout.
- PayU and PhonePe are prototypes, not registered.
- JioPay is an unregistered prototype.
- Paytm and CCAvenue are not implemented.
- No provider is production-ready; live collection is off.

Inspected paths: `payment-backend/src/providers/{factory,adapter-contract,razorpay,cashfree}.mjs`, `payment-backend/src/server.mjs`, `payment-backend/src/local-server.mjs`, `payment-backend/package.json`, and `docs/PROVIDER_INTEGRATION_STATUS.md`.

### feature/multi-provider-payment-backend
Later snapshot:
- Six seven-operation adapters are described: Razorpay, Cashfree, PayU, PhonePe, Paytm and JioPay.
- CCAvenue has a narrower hosted-checkout/status/encrypted-callback adapter; standard refund and refund-status operations are not supported in this snapshot.
- Registry exposes seven for test-mode checkout; live collection remains disabled.
- Provider acceptance evaluator validates evidence shape, but synthetic unit evidence is not merchant UAT.
- Reconciliation worker targets a Firestore folio/journal model not established as canonical PMS accounting schema.

Inspected paths:
`payment-backend/src/providers/{factory,registry,adapter-contract,razorpay,cashfree,payu-adapter,phonepe-adapter,paytm-adapter,jiopay-adapter,ccavenue}.mjs`,
`payment-backend/src/webhooks/provider-webhook-router.mjs`,
`payment-backend/src/intents/firestore-repository.mjs`,
`payment-backend/src/payment-controls/firestore-controls.mjs`,
`payment-backend/src/secrets/secret-version-management.mjs`,
`payment-backend/src/server.mjs`, `payment-backend/src/local-server.mjs`,
`src/payments/canonical.mjs`,
`docs/HYBRID_PAYMENT_ARCHITECTURE.md`,
`docs/PROVIDER_INTEGRATION_STATUS.md`,
`docs/SEVEN_PROVIDER_ACCEPTANCE.md`,
`docs/PAYMENT_IMPLEMENTATION_PLAN.md` (historical/superseded execution plan).

### backup/pre-settlement-command-gate-2026-11-08
This snapshot reports six seven-operation adapters and six Android checkout handoffs; CCAvenue is catalogue-only. It differs from the later multi-provider snapshot, which reports CCAvenue's narrower adapter as factory-wired and test-checkout-enabled. These are different source states and must not be merged into one claim.

### feature/direct-upi-reconciliation
README describes local UPI evidence parsing/matching foundations:
- Bank SMS parsing fails closed on unknown formats.
- IndexedDB evidence journal, event/UTR deduplication and review decisions.
- No production Android observer, production backend, connected review UI or atomic folio posting in that snapshot.
- Automatic folio settlement disabled.

### feature/pay-bridge-platform
README and `docs/ANDROID-INTEGRATION.md` describe separate companion app boundary:
- PMS package `com.aurelia.pms`; companion `com.aurelia.paybridge`.
- Bound Messenger/Binder IPC, signature permission, same signing certificate, package signer validation and nonce handshake.
- Companion APK intentionally excluded from PMS build.
- Handshake is not payment proof; payment execution/refunds/provider management were not implemented in the cited README snapshot.

### fix/payment-dispatch-boundary
README describes Android local credential vault foundations for Razorpay/Cashfree test/live credential sets and states no deployed cloud payment server is claimed. Pay Bridge is a separate repository.

### hardening/payment-appliance-durability-current
README describes local device + property LAN + Internet target topology and a separate Pay Bridge repository. Provider sandbox/live E2E, physical-device acceptance and local QR generation were not complete at that checkpoint.

### work/payment-ack-proof-hardening
README describes local-first PMS release gating: online gateway checkout, provider activation, reconciliation and Pay Bridge blocked at application entry points; offline manual cash/payment recording remains separate.

## Audit completeness boundary
All 74 branch names were enumerated, and payment-specific branches and relevant snapshots were inspected. The connected GitHub interface did not expose recursive Git tree enumeration for every branch. Therefore this is not a claim that every file in all 74 branches was exhaustively enumerated. A full clone or Git Trees API recursive scan and path/hash manifest is required before destructive removal.

## Provenance links
- [main](https://github.com/Akshayshetye37/Aurelia-apk/tree/main)
- [multi-provider backend](https://github.com/Akshayshetye37/Aurelia-apk/tree/feature/multi-provider-payment-backend)
- [production adapters](https://github.com/Akshayshetye37/Aurelia-apk/tree/feature/payment-provider-production-adapters)
- [direct UPI reconciliation](https://github.com/Akshayshetye37/Aurelia-apk/tree/feature/direct-upi-reconciliation)
- [Pay Bridge platform](https://github.com/Akshayshetye37/Aurelia-apk/tree/feature/pay-bridge-platform)
- [pre-settlement snapshot](https://github.com/Akshayshetye37/Aurelia-apk/tree/backup/pre-settlement-command-gate-2026-11-08)
