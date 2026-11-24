# Payment Gateway Information Build

Dedicated destination for payment-provider research and the planned standalone **Aurelia Pay** product.

## Separation objective

Aurelia PMS must not contain provider-specific gateway adapters, merchant credentials, provider checkout SDKs, provider webhook handlers or gateway administration. PMS remains responsible for property operations and its local financial records. Aurelia Pay will own provider connectivity and return authenticated, verified payment events through a narrow integration contract.

This repository currently contains source-derived research, branch provenance, provider capability inventory and migration boundaries. The provider source code has **not yet been moved**; this is not a completed code migration.

## Source
- [Aurelia-apk](https://github.com/Akshayshetye37/Aurelia-apk)
- [Multi-provider backend branch](https://github.com/Akshayshetye37/Aurelia-apk/tree/feature/multi-provider-payment-backend)
- [Production-adapter branch](https://github.com/Akshayshetye37/Aurelia-apk/tree/feature/payment-provider-production-adapters)
- [Direct UPI reconciliation branch](https://github.com/Akshayshetye37/Aurelia-apk/tree/feature/direct-upi-reconciliation)
- [Pay Bridge platform branch](https://github.com/Akshayshetye37/Aurelia-apk/tree/feature/pay-bridge-platform)

## Research documents
- [Source branch audit](docs/SOURCE_BRANCH_AUDIT.md)
- [Provider catalogue](docs/PROVIDER_CATALOGUE.md)
- [Architecture and security](docs/ARCHITECTURE_SECURITY.md)
- [PMS separation and migration boundary](docs/PMS_SEPARATION.md)

No provider is production-ready based on source presence alone. Merchant sandbox/UAT, deployed webhook verification, canonical accounting integration, device testing and formal release approval are separate gates. Live collection remains disabled.
