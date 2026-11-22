# Antigravity Master Architectural Plan & Decision Baseline

This document is the canonical reference and architectural ledger for all Antigravity execution phases (A0–A15), establishing the locked decisions and phased roadmap evolving Central Intelligence (CI) into Hermes-hosted.

---

## 1. Locked Architectural Decisions (D1–D17)

| Decision ID | Area | Decision Description | Status |
|:---|:---|:---|:---|
| **D1** | Core Architecture | 32 core components structured into 4 foundational tiers; strictly constructed in dependency order. | **Locked** |
| **D2** | Core Capabilities | Core explicitly incorporates the 6 pillar capabilities: Intelligent Gateway, Model Routing, Persistent Memory, Agent Orchestration, Automation, and RAG Database. | **Locked** |
| **D3** | Model Consumption | BYOK (Bring Your Own Key) only initially; managed pooled models and local/self-hosted inference enabled after foundational contracts are proven. | **Locked** |
| **D4** | Compute Runtime | Centrally managed resources initially; BYOI (Bring Your Own Infrastructure) enabled later via decoupled control plane + runner architecture. | **Locked** |
| **D5** | Vault & Secrets | Single unified ecosystem vault; default-deny permission grants per app/agent/workflow/tool; gateway-side runtime credential injection (models/agents never touch raw keys). | **Locked** |
| **D6** | Ledger & Billing | Append-only ledger; raw quantities tracked; explicit estimate labels; versioned price tables; component ID + version recorded on every transaction. Tracking only initially; transparent PAYG billing later. | **Locked** |
| **D7** | Recommender & Balances | User-declared balances; continuous burn-rate, runway, and drift tracking; recommender advises top-ups; user records top-ups; provider upstream rejection acts as real grounding signal. | **Locked** |
| **D8** | Pricing Model | Flat rate structure: predictable monthly subscriptions per user; annual enterprise licenses; zero per-event transaction fees or arbitrary markups. | **Locked** |
| **D9** | Entitlements | Entitlements evaluated per `(user, workspace)`; resolution precedence: Enterprise > Team > Personal > Overrides; workspace count treated as an explicit plan limit; all resolution sources logged. | **Locked** |
| **D10** | Staging Strategy | Strict staged rollout: Individual first (perfected), followed by Teams, followed by Enterprise. | **Locked** |
| **D11** | Sourcing Ladder | 4-level sourcing progression: **L1** Deploy fast (established market tools) -> **L2** OSS fusion/stitch -> **L3** Full parity with OSS & proprietary -> **L4** Custom implementations where justified; versioned contracts first; bug fixes in every phase. | **Locked** |
| **D12** | Release Lanes | Multi-lane concurrent progression: L1 launches first; L2 develops while L1 runs; Lane N (Production), Lane N+1 (Testing), Lane N+2 (Development); stable intermediate releases. | **Locked** |
| **D13** | Work Cycle | Iterative 6-stage lifecycle per component: Plan -> Design -> Develop -> Test -> Deploy -> Review; safe stage overlap allowed. | **Locked** |
| **D14** | Component Catalog | Components function as catalog items; products and workspaces are modular compositions; components crossing parity thresholds are automatically selected into user workspaces. | **Locked** |
| **D15** | Release Evolution | Independent component versioning and evolution; strategic staged rollouts; every release demonstrates empirical, measured improvements backed by evidence. | **Locked** |
| **D16** | Metric Validation | Both feature expansion and resource optimization qualify as improvements; measured and reported separately; strict rule: *no improvement claim without a same-conditions benchmark against prior baseline*. | **Locked** |
| **D17** | Execution Defaults | Operational defaults: Lane WIP limit of 2 in development / 1 in testing; minimum 20% bug-fix/hardening budget per cycle; strict exit criteria; "better" bar significantly above statistical noise. | **Locked** |

---

## 2. Antigravity Phased Execution Roadmap (A0–A15)

```
[ A0 / Prompt 1: Tell Bootstrap + Sync Workflow ]
                       |
                       v
[ Prompt 2 / A1: Postgres Multi-Tenant Consolidation ]
                       |
                       v
[ A2: Valkey / Redis Consolidation ] -----------------> [ A3: Zero-Container Embedded Fallbacks ]
                       |                                                   |
                       +---------------------------------------------------+
                       |
                       v
[ A4: Observability Partitioning (SigNoz/ClickHouse) ]
                       |
                       v
[ A5: Billing & Metering Decoupling (Lago/OpenMeter) ]
                       |
                       v
[ A6: Event Streaming & Kafka Decoupling ]
                       |
                       v
[ A7: Vector Datastore Dual-Mode (Qdrant / pgvector) ]
                       |
                       v
[ A8: Graph Engine On-Demand Activation (Apache AGE) ]
                       |
                       v
[ A9: Surface & UI Decoupling (LibreChat & Hermes UI) ]
                       |
                       v
[ A10: Identity & ReBAC Policy Consolidation (Keycloak / OpenFGA) ]
                       |
                       v
[ A11: Secrets Keystore Hardening (Vault / Local Vault) ]
                       |
                       v
[ A12: Workflow Orchestration Optimization (Temporal) ]
                       |
                       v
[ A13: Hermes Multi-Tenant Routing & Gateway Layer ]
                       |
                       v
[ A14: Multi-Tenant Isolation & Test Gate Verification ]
                       |
                       v
[ A15: Production Release, Packaging & Codespace Signoff ]
```

### Phase Details

#### A0 / Prompt 1: Tell Bootstrap + Sync Workflow (Current)
- **Goal:** Vendor CI into downstream product repository `Aashish-RC/tell` under `ci/` via `git subtree`.
- **Deliverables:** `.devcontainer/devcontainer.json`, `SYNC.md`, `hermes/` product scaffold, `README.md`, verified Codespace startup.

#### Prompt 2 / A1: Postgres Database Consolidation
- **Goal:** Consolidate 5 disparate PostgreSQL instances (`openfga-postgres`, `infisical-postgres`, `lago-postgres`, `openmeter-postgres`, `signoz-metastore-postgres-0`) into the primary `supabase-db` instance using `compose/postgres/init-multi-db.sh`.
- **Impact:** Eliminates 3 to 5 containers; saves ~700 MB RAM and ~1.1 GB disk.

#### A2: Valkey / Redis Cache Consolidation
- **Goal:** Merge `infisical-redis`, `lago-redis`, and `openmeter-redis` into logical databases (DB 0–3) on the primary OSI-compliant `valkey` container.
- **Impact:** Eliminates 3 containers; purges non-OSI (RSALv2/SSPL) Redis licenses from the default stack.

#### A3: Zero-Container Embedded Fallbacks (Lite Profile)
- **Goal:** Activate native zero-container embedded fallbacks:
  - OpenFGA -> SQLite Zanzibar recursive CTE engine.
  - Infisical -> In-process AES-256-GCM local keystore.
  - Temporal -> In-process deterministic DAG checkpoint engine.
  - Apache AGE -> NetworkX in-memory graph adapter.
- **Impact:** Enables full cognitive development on 4–8 GB machines with 3 containers and ~300 MiB idle RAM.

#### A4: Observability Partitioning
- **Goal:** Decouple SigNoz APM and ClickHouse into `--profile enterprise` / `--profile observability`.
- **Impact:** Eliminates 7 heavy containers from standard developer workflow; saves ~850 MiB RAM.

#### A5: Billing & Metering Decoupling
- **Goal:** Profile-partition Lago and OpenMeter. Deploy built-in Python metering engine for Lite/Standard; retain distributed ClickHouse/OLAP for Enterprise.
- **Impact:** Saves ~1,200 MiB idle RAM in Lite and ~650 MiB in Standard.

#### A6: Event Streaming & Message Queue Decoupling
- **Goal:** Decouple JVM Apache Kafka for non-enterprise environments. Replace with direct Valkey Streams or synchronous HTTP ingestion.
- **Impact:** Eliminates JVM memory overhead and prevents container OOM exit code 137.

#### A7: Vector Datastore Dual-Mode
- **Goal:** Enable dual-mode cognitive vector storage: Qdrant HNSW engine for heavy production workloads; pgvector (`supabase-db`) for lightweight single-node deployments.
- **Impact:** Saves 1 container and ~54 MiB idle RAM on lightweight nodes.

#### A8: Graph Engine On-Demand Activation
- **Goal:** Partition Apache AGE graph database to on-demand activation, utilizing SQLite/NetworkX edge tables for routine agent memory.
- **Impact:** Saves ~91 MiB idle RAM when graph deep traversals are inactive.

#### A9: Surface & UI Decoupling
- **Goal:** Move LibreChat and SSPL MongoDB to `--profile ui`. Build out `hermes/surface` modern client interface.
- **Impact:** Headless core with web console; complete removal of SSPL licenses from default runtime.

#### A10: Identity & Access Management Hardening
- **Goal:** Unify Keycloak multi-tenant realms with OpenFGA ReBAC relationships for seamless organization and role management.
- **Impact:** Zero-trust tenant isolation across all cognitive workflows.

#### A11: Secrets Keystore Hardening
- **Goal:** Standardize unified secrets retrieval interface across external Infisical and local encrypted key vaults.
- **Impact:** Secure runtime key injection with zero credential leakage in traces or agent execution contexts.

#### A12: Workflow Orchestration Hardening
- **Goal:** Enforce deterministic replay boundaries, cgroup memory limits (256 MB), and SQLite persistence for Temporal.
- **Impact:** Bulletproof long-running autonomous agent workflows with bounded resource utilization.

#### A13: Hermes Multi-Tenant Routing & Gateway Layer
- **Goal:** Populate `hermes/router` with the Layer 4 intelligent gateway, tenant rate-limiting, and internal RPC dispatch into the CI Primary Brain.
- **Impact:** Secure commercial PaaS/SaaS wrapper with per-tenant secondary brain management.

#### A14: Multi-Tenant Isolation & Test Gate Verification
- **Goal:** Execute comprehensive automated test gates across Lite, Standard, and Enterprise profiles.
- **Impact:** Formal proof of tenant isolation, performance bounds, and zero regression across the matrix.

#### A15: Production Release, Packaging & Codespace Signoff
- **Goal:** Finalize production Helm charts, air-gapped Docker bundles, one-click Codespaces onboarding, and release documentation.
- **Impact:** Hermes-hosted production general availability (GA).
