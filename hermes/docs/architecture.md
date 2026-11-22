# Hermes vs Central Intelligence Architecture

## 1. Architectural Philosophy
Hermes is the downstream product shell, SaaS/PaaS delivery platform, and multi-tenant runtime that evolves **Central Intelligence (CI)** into a commercially deployable, hosted system.

```
+-------------------------------------------------------------------------+
|                              END USER                                   |
+-------------------------------------------------------------------------+
                                    |
                                    v (HTTPS / WSS)
+-------------------------------------------------------------------------+
|                           HERMES PRODUCT LAYER                          |
|                                                                         |
|  [ Configurable Surface ] <---> [ Hermes Router ]                       |
|         (A9)                            |                               |
|                                         v                               |
|                     [ Per-Tenant Secondary Brain ]                      |
|                     - Light tenant isolation runtime                    |
|                     - User session context & preferences                |
|                     - Custom prompt & tool policies                     |
+-------------------------------------------------------------------------+
                                    |
                                    v (Internal RPC / Authenticated mTLS)
+-------------------------------------------------------------------------+
|                  CENTRAL INTELLIGENCE (PRIMARY BRAIN)                   |
|                                                                         |
|  * Never directly exposed to end users                                  |
|  * Durable memory (Supabase, Qdrant, Apache AGE)                        |
|  * Heavy cognitive orchestration & reasoning (Temporal, Valkey)         |
|  * Hard security boundaries (OpenFGA, Infisical / Local Vault)          |
|  * Telemetry & Accounting (SigNoz APM, Lago billing, OpenMeter)         |
+-------------------------------------------------------------------------+
```

## 2. Core Separation of Responsibilities

### Primary Brain: Central Intelligence (`ci/`)
- **Role:** Deep cognitive infrastructure, knowledge ingestion, durable state machines, and relational/vector/graph storage.
- **Exposure:** **Strictly Internal.** Never directly addressed or accessible by external end users.
- **Codebase Source:** Maintained in upstream repository `Aashish-RC/Central-Intelligence` and mirrored downstream via `git subtree` into `ci/`.

### Secondary Brain: Hermes Product Layer (`hermes/`)
- **Role:** A lightweight, per-tenant runtime serving end users.
- **Surface (`hermes/surface`):** The configurable user interface, client applications, web dashboard, and tenant customization layer.
- **Router (`hermes/router`):** Intelligent gateway handling tenant token metering, load balancing, model routing, and authenticated RPC dispatch into the Central Intelligence Primary Brain.
- **Tenant Isolation:** Each tenant operates within a dedicated context in the secondary brain, while the primary brain coordinates execution deterministically across consolidated backing datastores.
