# Changelog
All notable changes to this project will be documented in this file.

## [1.0.0] - 2026-07-18
Initial release. n8n Cloud–verifiable edition of the Hudu node, derived from n8n-nodes-hudu 2.7.0.

- Zero runtime dependencies, qualifying this package for n8n Cloud community-node verification.
- Includes the regular `Hudu` node with full resource/operation coverage (Companies, Assets, Articles, Passwords, Networks, VLANs, Users, and more).
- Retains `usableAsTool: true` on the `Hudu` node, so it can still be connected directly to an n8n AI Agent node — no separate tool node required.
- Does not include the dedicated `HuduAiTools` / MCP Server Trigger tool node. For that functionality, use [n8n-nodes-hudu](https://github.com/msoukhomlinov/n8n-nodes-hudu) (self-hosted only).
- Get Many "Limit" fields default to 50, per n8n Cloud community-node verification rules.

"Cloud" in this package's name refers to **n8n Cloud** (the hosted n8n platform), not Hudu's own hosting.
