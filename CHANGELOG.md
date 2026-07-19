# Changelog
All notable changes to this project will be documented in this file.

## [2.0.0] - 2026-07-20

### Changed (breaking)
- **Package renamed `n8n-nodes-hudu-cloud` → `n8n-nodes-hudu-core`.** The old package is deprecated on npm; install `n8n-nodes-hudu-core` going forward.
- **Renamed the node and credential types so this edition can be installed alongside the full [n8n-nodes-hudu](https://github.com/msoukhomlinov/n8n-nodes-hudu) package on the same n8n instance.** The node is now **"Hudu Core"** (internal type `huduCore`, file `HuduCore.node.ts`) and the credential **"Hudu Core API"** (internal type `huduCoreApi`). Previously both were `hudu` / `huduApi`, which collided with the full package — n8n keys installed node and credential types by name, so the two could not co-exist.
- **Breaking:** 1.0.0 (published as `n8n-nodes-hudu-cloud`) used the `hudu` / `huduApi` types; existing installs, workflows, and credentials will not carry over. Install `n8n-nodes-hudu-core`, add the **Hudu Core** node, and create a **Hudu Core API** credential. (1.0.0 had negligible installs.)

## [1.0.0] - 2026-07-18
Initial release. n8n Cloud–verifiable edition of the Hudu node, derived from n8n-nodes-hudu 2.7.0.

- Zero runtime dependencies, qualifying this package for n8n Cloud community-node verification.
- Includes the regular `Hudu` node with full resource/operation coverage (Companies, Assets, Articles, Passwords, Networks, VLANs, Users, and more).
- Retains `usableAsTool: true` on the `Hudu` node, so it can still be connected directly to an n8n AI Agent node — no separate tool node required.
- Does not include the dedicated `HuduAiTools` / MCP Server Trigger tool node. For that functionality, use [n8n-nodes-hudu](https://github.com/msoukhomlinov/n8n-nodes-hudu) (self-hosted only).
- Get Many "Limit" fields default to 50, per n8n Cloud community-node verification rules.

"Cloud" in this package's name refers to **n8n Cloud** (the hosted n8n platform), not Hudu's own hosting.
