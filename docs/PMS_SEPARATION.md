# Aurelia PMS separation and migration boundary

## Target state
Aurelia PMS becomes free of provider-specific gateway integrations. It retains hospitality operations and its local accounting/payment records. The independent Aurelia Pay service owns provider adapters, merchant credentials, checkout sessions, webhook ingress, provider status verification, refunds, provider reconciliation and gateway operations.

PMS communicates with Aurelia Pay only through a versioned, authenticated, minimal contract.

## PMS must retain
- Booking, guest, room, folio and invoice domains.
- Local cash/manual payment recording where operationally required.
- Its own immutable financial ledger and audit history.
- A minimal external payment-reference mapping for booking/folio reconciliation.
- Fail-closed handling of unavailable or untrusted remote events.

## PMS must not retain
- Provider API keys, salts or webhook secrets.
- Provider SDKs and provider-specific checkout protocol logic.
- Provider-specific webhook/callback handlers.
- Gateway administration and credential configuration screens.

## Migration rules
1. Build and qualify Aurelia Pay independently before removing still-required PMS behavior.
2. Export provider source with path/hash manifest and branch provenance.
3. Move provider-specific files and provider-only dependencies; preserve licenses and notices.
4. Replace PMS provider code with a small provider-neutral client contract only after Aurelia Pay is operationally qualified.
5. Preserve payment history, folio entries, booking amounts, idempotency IDs and audit records. Do not rewrite historical values.
6. Keep offline cash/manual payment workflows separate.
7. Add contract tests proving PMS cannot call provider endpoints directly and no provider credentials/SDK dependencies remain in PMS bundles.
8. Remove provider-only source/dependencies from maintained PMS branches after destination verification.
9. Do not rewrite every historical Git commit or delete recovery branches in the first migration. Historical snapshots remain audit/recovery records; maintained release branches must be clean.
10. Verify PMS build, dependency scan, secret scan, provider-reference scan, offline workflows and ledger regressions.

## Actual migration status
This repository currently contains source-derived inventory and architecture documents. Provider adapter/backend source has **not yet been transferred**, and no source files have yet been deleted from Aurelia-apk. Recursive tree enumeration of all 74 source branches and a path/hash manifest are prerequisites to destructive removal.
