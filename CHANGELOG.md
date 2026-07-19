# Changelog
All notable changes to this project will be documented in this file.

## [2.0.0] - 2026-07-19

### Changed (breaking)
- **Renamed the node and credential types so this edition can be installed alongside the full [n8n-nodes-hudu](https://github.com/msoukhomlinov/n8n-nodes-hudu) package on the same n8n instance.** The node is now **"Hudu (n8n Cloud)"** (internal type `huduCloud`) and the credential is **"Hudu Verified API"** (internal type `huduCloudApi`); the node file is `HuduCloud.node.ts`. Previously both were `hudu` / `huduApi`, which collided with the full package (n8n keys installed node and credential types by name, so the two could not co-exist).
- **Breaking:** existing workflows and credentials created against the 1.0.0 `hudu` / `huduApi` types will not carry over — re-add the "Hudu (n8n Cloud)" node and re-create the "Hudu Verified API" credential. (1.0.0 had negligible installs.)

## [1.0.0] - 2026-07-18
Initial release. n8n Cloud–verifiable edition of the Hudu node, derived from n8n-nodes-hudu 2.7.0.

- Zero runtime dependencies, qualifying this package for n8n Cloud community-node verification.
- Includes the regular `Hudu` node with full resource/operation coverage (Companies, Assets, Articles, Passwords, Networks, VLANs, Users, and more).
- Retains `usableAsTool: true` on the `Hudu` node, so it can still be connected directly to an n8n AI Agent node — no separate tool node required.
- Does not include the dedicated `HuduAiTools` / MCP Server Trigger tool node. For that functionality, use [n8n-nodes-hudu](https://github.com/msoukhomlinov/n8n-nodes-hudu) (self-hosted only).
- Get Many "Limit" fields default to 50, per n8n Cloud community-node verification rules.

"Cloud" in this package's name refers to **n8n Cloud** (the hosted n8n platform), not Hudu's own hosting.
