# CI ↔ tell sync workflow

## Repository roles
- **CI repo (`Aashish-RC/Central-Intelligence`)**: upstream brain. All code changes to the cognitive/control/platform/surface layers happen here first. Local working copy: `<CI_REPO_PATH>` (`d:\central-intelligence`).
- **tell repo (`Aashish-RC/tell`)**: downstream product repo. Mirrors CI at `ci/` (via git subtree) and adds the Hermes product layer at the root. Local working copy: `<TELL_REPO_PATH>` (`d:\tell`).

## One-time setup (already done in Prompt 1)
```bash
git subtree add --prefix=ci/ https://github.com/Aashish-RC/Central-Intelligence.git main --squash
```

## Per-phase workflow (every Antigravity prompt from Prompt 2 onward)
1. Make changes on the local CI repo at `<CI_REPO_PATH>`.
2. Run tests locally on the CI repo.
3. Commit + push to `https://github.com/Aashish-RC/Central-Intelligence.git` (`origin main`).
4. In the local tell repo at `<TELL_REPO_PATH>`:
   ```bash
   git subtree pull --prefix=ci/ https://github.com/Aashish-RC/Central-Intelligence.git main --squash
   ```
   *(this fetches the latest CI commits and merges them into tell's `ci/` directory)*
5. Resolve any conflicts (expected: none, because Hermes layer is outside `ci/`).
6. Commit + push to `https://github.com/Aashish-RC/tell.git` (`origin main`).
7. Open a Codespace on the tell repo (GitHub UI → Code → Codespaces → Create on main, or `gh codespace create -r Aashish-RC/tell --machine standardLinux`).
8. In the Codespace (working dir `/workspaces/tell`):
   ```bash
   cd ci/
   cp .env.example .env # then edit to e2e-* defaults
   docker compose --profile core up -d
   pytest -m "live and not requires_provisioned_services" tests/integration/test_compose_stack_e2e.py -v
   ```
9. Capture test output to `/home/z/my-project/download/_codespace_results.txt` (or `/workspaces/tell/promptX_codespace_results.txt`).
10. Tear down:
    ```bash
    docker compose --profile core down
    ```

## Pushing tell-side fixes back to CI (rare)
If a fix is needed in CI files while working in tell:
1. Make the fix directly in `<CI_REPO_PATH>` (not in `tell/ci/`).
2. Push to CI repo.
3. In tell:
   ```bash
   git subtree pull --prefix=ci/ https://github.com/Aashish-RC/Central-Intelligence.git main --squash
   ```
   to sync.

*Never edit files under `tell/ci/` directly — always edit in the CI repo and sync.*
