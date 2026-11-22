# Hermes

> **Hermes** is the commercial SaaS/PaaS product and hosted runtime layer built on top of [Central Intelligence (CI)](https://github.com/Aashish-RC/Central-Intelligence) as its primary cognitive brain.

---

## 1. Overview
Central Intelligence serves as the primary brain — housing deep cognitive memory, multi-agent orchestration, durable workflows, and relational/vector/graph stores. Hermes provides the outer product layer:
- **Hermes Router:** Multi-tenant intelligent gateway, token metering, model routing, and authenticated RPC proxy.
- **Hermes Surface:** Configurable client interfaces, management consoles, and tenant workspace experiences.
- **Secondary Brain:** Lightweight, per-tenant runtime isolating user preferences, custom prompt contexts, and private session history.

---

## 2. Repository Layout

```
.
├── .devcontainer/         # Codespaces configuration (Docker-in-Docker, Python 3.10, Node 20)
├── ci/                    # Central Intelligence upstream codebase (vendored via git subtree)
├── docs/
│   └── antigravity_plan.md# Master plan: Locked decisions D1-D17 & phased roadmap A0-A15
├── hermes/                # Hermes product layer scaffold
│   ├── docs/
│   │   └── architecture.md# Primary brain vs secondary brain architectural separation
│   ├── router/            # Intelligent router & gateway (populated in Phase A13)
│   └── surface/           # Configurable UI & client surface (populated in Phase A9)
├── README.md              # Product orientation & documentation
└── SYNC.md                # Upstream CI <-> tell git subtree synchronization workflow
```

---

## 3. Cloud Development in GitHub Codespaces
This repository is configured for immediate development inside **GitHub Codespaces**:
- Standard machine: **4 vCPU / 16 GB RAM / 32 GB disk**.
- Pre-installed with Docker-in-Docker, Python 3.10, Node.js 20, pnpm, and GitHub CLI.
- Automatically installs required dependencies upon container creation.

To launch:
1. Navigate to the [GitHub Repository](https://github.com/Aashish-RC/tell).
2. Click **Code** -> **Codespaces** -> **Create codespace on main**.
3. Or launch via GitHub CLI:
   ```bash
   gh codespace create -r Aashish-RC/tell --machine standardLinux
   ```

To run the stack inside the Codespace:
```bash
cd /workspaces/tell/ci
cp .env.example .env
# Edit .env with your environment configuration or test defaults
docker compose --profile core up -d
pytest -m "live and not requires_provisioned_services" tests/integration/test_compose_stack_e2e.py -v
```

---

## 4. Upstream Synchronization Workflow
All cognitive and platform changes are developed upstream in `Central-Intelligence` and pulled into `tell`:
- Detailed instructions are documented in [SYNC.md](file:///d:/tell/SYNC.md).
- To sync downstream:
  ```bash
  git subtree pull --prefix=ci/ https://github.com/Aashish-RC/Central-Intelligence.git main --squash
  ```

---

## 5. Development Status
- **Current Status:** Pre-Alpha (Antigravity bootstrap complete).
- **Active Roadmap:** Phased evolution from A0 through A15 in [docs/antigravity_plan.md](file:///d:/tell/docs/antigravity_plan.md).
